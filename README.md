<p align="center">
  <img src="assets/readme-lume.gif" alt="Lume Sentinel — segurança local com IA" width="900">
</p>

<p align="center">
  <strong>Monitoramento local, análise assistida pelo Ollama e ações sob seu controle.</strong>
</p>

<p align="center">
  <a href="#visao-geral">Visão geral</a> ·
  <a href="#instalacao">Instalação</a> ·
  <a href="#modelos">Modelos</a> ·
  <a href="#limites-e-seguranca">Limites e segurança</a> ·
  <a href="https://github.com/pedroyan2310-ctrl/lume_sentinel">Repositório</a>
</p>

## Visão geral

A **Lume Sentinel** é um painel Windows que acompanha eventos locais de arquivos, conexões de rede e processos elevados. Ela pode encaminhar metadados de eventos para um modelo instalado no [Ollama](https://ollama.com/) e apresentar uma avaliação com recomendações.

O projeto separa o trabalho em três partes:

1. **Sensores:** observam as pastas configuradas, novas conexões públicas e novos processos elevados.
2. **Análise:** envia ao Ollama local um JSON com os metadados do evento para obter uma leitura e uma recomendação.
3. **Resposta:** deixa você analisar o alerta e decidir se bloqueia um IP, coloca um arquivo permitido em quarentena ou encerra um processo elegível.

## Instalação

### Requisitos

- Windows 10 ou posterior.
- [Ollama para Windows](https://ollama.com/download/windows), aberto durante o uso da análise por IA.
- Python 3.10 ou posterior e conexão à internet na primeira instalação das dependências.
- Espaço em disco para o modelo escolhido. O download do modelo é separado do pacote Lume.

### Baixar e iniciar

1. Baixe `downloads/Lume-Sentinel-2.0.zip` ou o ZIP da versão publicada na página do projeto.
2. Extraia o arquivo para uma pasta onde sua conta tenha permissão de gravação.
3. Abra o PowerShell e baixe um modelo (há opções abaixo):

   ```powershell
   ollama pull qwen3.5:2b-q4_K_M
   ```

4. Crie a pasta de projetos usada pelo exemplo de configuração:

   ```powershell
   New-Item -ItemType Directory -Force "$HOME\Documents\Lume Sentinel\Projetos" | Out-Null
   ```

5. Execute `instalar-dependencias.bat` uma vez.
6. Execute `iniciar-lume.bat`, escolha o modelo no painel e clique em **Iniciar sensores**.

O painel usa `127.0.0.1` por padrão. Os eventos são mantidos na pasta `data` do aplicativo; revise e remova esses registros localmente quando não precisar mais deles.

### Permissões de administrador

Abra o inicializador como **Administrador** quando quiser permitir consultas e respostas que exigem elevação, como criar uma regra no Firewall do Windows. Sem elevação, a interface e partes do monitoramento ainda podem funcionar, mas o Windows pode negar algumas operações. Nenhum modo garante visibilidade ou proteção total.

## Modelos Ollama

Escolha um modelo instalado na lista do painel. Para baixar um modelo pelo PowerShell:

```powershell
ollama pull qwen3.5:2b-q4_K_M
```

Outras opções leves para experimentar:

```powershell
ollama pull qwen3.5:0.8b
ollama pull qwen3:1.7b
ollama pull deepseek-r1:1.5b
ollama pull gemma3:1b
ollama pull llama3.2:3b
```

O tamanho real e os requisitos dependem da quantização e da versão do modelo. Com 16 GB de RAM e uma RX 550 de 4 GB, comece por um modelo pequeno; o Ollama pode usar CPU se a aceleração da placa não estiver disponível. Consulte a biblioteca oficial do Ollama para conferir as variantes atuais.

## Capturas de tela

As imagens abaixo mostram a interface do aplicativo:

<p align="center">
  <img src="assets/gallery/lume-dashboard.png" alt="Visão geral da Lume Sentinel" width="900">
</p>

<p align="center">
  <img src="assets/gallery/lume-monitoramento.png" alt="Eventos do monitoramento local" width="900">
</p>

<p align="center">
  <img src="assets/gallery/lume-arquivos.png" alt="Eventos de arquivos" width="900">
</p>

## Limites e segurança

- A Lume **não é um antivírus certificado, EDR ou ferramenta de resposta garantida**. Um alerta não prova que há malware; software legítimo pode gerar eventos parecidos.
- A versão atual é um aplicativo Python em **espaço de usuário**. Ela **não roda no nível do kernel** e não instala um driver de kernel. Executar como Administrador não muda isso.
- O sensor de arquivos registra metadados e hash de EXE/DLL nas pastas configuradas; ele não examina o conteúdo de todos os arquivos.
- A análise usa o modelo selecionado no Ollama. Por padrão, os metadados seguem para `127.0.0.1`; se você alterar `OLLAMA_HOST`, serão enviados ao endpoint configurado por você.
- A resposta automática começa desligada. Se armada, pode executar ações elegíveis para alertas avaliados como risco alto/crítico. A classificação e a confiança são geradas pelo modelo e podem estar erradas.
- Uma regra criada no Firewall do Windows permanece ativa até ser removida pelo painel ou pelas ferramentas do Firewall. Confira as ações antes de armar a resposta automática.
- A quarentena está limitada às pastas de projeto permitidas na configuração. Mantenha uma cópia de segurança dos seus arquivos.

Para habilitar monitoramento real em nível de kernel seria necessário desenvolver e testar um driver Windows assinado; essa capacidade não faz parte deste projeto.

## Estrutura deste repositório

```text
index.html                 Site público estático
assets/gallery/            Capturas da aplicação
assets/readme-lume.gif     Cabeçalho animado deste README
downloads/                 Pacote Windows para download
```

O site não precisa de etapa de build: `index.html` é servido diretamente. Para visualizar localmente:

```powershell
py -m http.server 8080
```

Depois abra `http://127.0.0.1:8080`. A animação da interface respeita a preferência do sistema por movimento reduzido.

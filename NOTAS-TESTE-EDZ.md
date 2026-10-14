# Notas de teste — EDZ

Teste da versão Linux do OpenJarvis feito em 14/10/2026, num container Linux
sem GPU (4 núcleos de CPU, 15 GB de RAM), a partir do commit `74694cd` do
repositório original ([open-jarvis/OpenJarvis](https://github.com/open-jarvis/OpenJarvis)).

## Instalação

```bash
curl -fsSL https://open-jarvis.github.io/OpenJarvis/install.sh | bash -s -- --no-analytics
```

Observações:

- O instalador **não roda como root**. Em ambientes que só têm root, crie um
  usuário comum (`useradd -m jarvis`) e rode por ele.
- O instalador do Ollama exige o `zstd` (`apt-get install zstd`). Sem root
  para instalar o Ollama, instale-o antes como administrador.
- `--no-analytics` recusa o envio de estatísticas de uso sem perguntar.
- `--no-bg-orchestrator` pula os downloads em segundo plano (extensão em Rust
  e modelos maiores). Sem a extensão, aparece o aviso
  `No module named 'openjarvis_rust'`, que não impede o uso.

Modelo padrão instalado: `qwen3.5:2b` (via Ollama).

## Resultados

`jarvis doctor`: 10 ok, 21 avisos (componentes opcionais), 0 falhas.

| Teste | Comando | Resultado | Tempo |
|---|---|---|---|
| Pergunta simples | `jarvis ask -e ollama "..."` | Resposta correta em português | ~29 s (2,1 tokens/s) |
| Agente com código | `jarvis ask -e ollama -a orchestrator --tools code_interpreter "..."` | 37 dias = 3.196.800 s (correto) | ~23 s |

A lentidão vem da falta de GPU. Num PC com placa de vídeo é bem mais rápido.

## No Windows

```powershell
irm https://open-jarvis.github.io/OpenJarvis/install.ps1 | iex
```

Requer Python 3.10–3.13, git e MSVC Build Tools. Alternativa mais simples: o
instalador `.exe` na página de
[releases](https://github.com/open-jarvis/OpenJarvis/releases).

O app desktop do Windows não traz o Ollama. Se aparecer "Could not start
Ollama", instale-o (`winget install Ollama.Ollama`) e reabra o app.

## Ligação com o original

Este repositório é uma cópia de
[open-jarvis/OpenJarvis](https://github.com/open-jarvis/OpenJarvis), não um
fork do GitHub. A automação `.github/workflows/sync-upstream.yml` puxa as
novidades do original todo dia (ou à mão em Actions → Sync upstream → Run
workflow).

Para instalar a partir **deste** repositório em vez do original:

```powershell
# Windows
$env:OPENJARVIS_REPO_URL = 'https://github.com/elieldnz/OpenJarvis.git'
irm https://open-jarvis.github.io/OpenJarvis/install.ps1 | iex
```

```bash
# Linux / macOS / WSL2
curl -fsSL https://open-jarvis.github.io/OpenJarvis/install.sh \
  | OPENJARVIS_REPO_URL=https://github.com/elieldnz/OpenJarvis.git bash
```

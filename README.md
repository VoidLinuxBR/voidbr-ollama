<div align="center">

# 🔵 voidbr-ollama

**Ollama - roda modelos de linguagem localmente (binário oficial, CPU/Vulkan + serviço runit)**

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg?style=for-the-badge)](LICENSE)

</div>

---

[Ollama](https://github.com/ollama/ollama) empacotado para o **VoidBR Linux**: o binário
oficial do projeto, com **serviço runit** no lugar do systemd.

O Ollama não está no repositório oficial do Void, e o instalador dele (`install.sh`)
instala em `/usr/local` e cria um serviço systemd — por isso este pacote.

## O que instala

| Caminho | Conteúdo |
|---|---|
| `/usr/bin/ollama` | binário oficial (release do GitHub, sha256 conferido) |
| `/usr/lib/ollama/` | bibliotecas do Ollama: CPU e Vulkan (~100 MB no total) |
| `/etc/sv/ollama/` | serviço runit (`run`, `log/run` com vlogger, `conf`) |
| `/var/lib/ollama/models` | modelos baixados (usuário de sistema `ollama`) |

Placa **NVIDIA**? Instale também o [voidbr-ollama-cuda](https://github.com/voidlinuxbr/voidbr-ollama-cuda)
(bibliotecas CUDA, ~2,1 GB), que vem do mesmo tarball oficial. Ficam separadas para
quem não tem NVIDIA não instalar 2 GB à toa.

## Uso

```bash
sudo ln -s /etc/sv/ollama /var/service/    # habilita e inicia
ollama pull qwen3:4b                        # baixa um modelo
ollama run qwen3:4b                         # conversa no terminal
```

Ou deixe o **VoidBR AI** fazer tudo: `voidbr-ai --setup-ai` (ou ☰ → Configurar IA local).

A API escuta só em `127.0.0.1:11434`. Para mudar (ou expor na rede local), edite
`/etc/sv/ollama/conf` e rode `sudo sv restart ollama`.

## Observações

- Só `x86_64` e `aarch64` com **glibc** (o binário oficial não roda em musl).
- NVIDIA: `voidbr-ollama-cuda` (+ driver `nvidia`). AMD/Intel: aceleração por Vulkan com
  `OLLAMA_VULKAN=1` em `/etc/sv/ollama/conf` (precisa do `vulkan-loader` e do driver Vulkan).
  ROCm (AMD) precisa do tarball `-rocm`, que fica para um pacote à parte.
- O serviço roda como o usuário `ollama`, com os grupos `video` e `render` (acesso à GPU).

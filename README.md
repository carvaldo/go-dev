# Go Build Container

Container Docker para compilar projetos Go de forma padronizada, sem necessidade de instalar o Go na maquina local.

## O que esta imagem faz

- Disponibiliza o compilador Go dentro de um ambiente isolado
- Usa o codigo-fonte montado por volume no caminho `/src`
- Executa comandos Go diretamente (`ENTRYPOINT ["go"]`)

Esta imagem e voltada para build, testes e tarefas de desenvolvimento. Nao e uma imagem de runtime para producao.

## Exemplo com Docker Compose

Exemplo de servico no `docker-compose.yml`:

```yaml
services:
  go:
    image: carvaldo/go-dev:latest
    volumes:
      - ./:/src
    environment:
      - CGO_ENABLED=0
      - GOOS=linux
      - GOARCH=amd64
```

Com isso, o comando abaixo compila o projeto atual:

```bash
docker compose run --rm go build -trimpath -o /src/app.bin .
```

## Variaveis de ambiente comuns

- `CGO_ENABLED`: habilita (`1`) ou desabilita (`0`) CGO*
- `GOOS`: sistema operacional de destino (ex.: `linux`, `windows`, `darwin`)
- `GOARCH`: arquitetura de destino (ex.: `amd64`, `arm64`)
- `GOEXE`: sufixo do executavel (ex.: `.exe` no Windows)

> *CGO: recurso que permite ao Go chamar codigo C (bibliotecas nativas). Em geral, `CGO_ENABLED=0` gera binarios mais portaveis e simples de distribuir; use `CGO_ENABLED=1` apenas quando seu projeto depender de bibliotecas C/sistema.

## Build otimizado (opcional)

Para reduzir o tamanho final do binario e remover metadados desnecessarios:

```bash
docker compose run --rm go build -trimpath -ldflags="-s -w" -o /src/app.bin .
```

> -trimpath: remove caminhos absolutos do código-fonte do binário. O que deixa build mais reprodutível e reduz vazamento de info do ambiente.

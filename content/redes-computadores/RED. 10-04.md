---
title: RED - Aula 10/04
draft: false
tags:
  - RED
date: 2025-04-10
---
# Protocolo HTTP e a Web

## HTTP/1.1

- ***Pipelining*** e bloqueio ***head of line* (HOL).**
	- Requisições em *pipelining* devem ser respondidas na mesma ordem.
		- As requisições podem ocorrer paralelamente.
	- Bloqueio HOL - quando um dos pedaços da mensagem não chega à aplicação. (Ex.: chegaram os pacotes 1, 2, 3 e 5, mas o pacote 4 está faltando).
		- A passagem de *bytes* ao programa é interrompida até que a sequência certa possa ser enviada.
	- O *pipelining* não é habilitado por padrão na maioria dos clientes *web*.
		- *Proxies web* não tratam corretamente.
		- Alguns servidores não respondem corretamente.

```
cliente                              servidor
send(buffer) < - - - - - - - - - - > receive()
```

- **Mal uso das conexões TCP.**
	- Controle de congestionamento (baixa velocidade).
	- Bloqueio *head of line*.
	- Perda de pacotes.
	- Dificuldade de substituir o TCP pelo SCTP.
	- Solução: melhorar o uso da aplicação
		- Múltiplas conexões TCP;
		- Heurísticas para reduzir o tempo de carregamento das páginas (HTTP/1.1);
		- Multiplexação da mesma conexão TCP (HTTP/2) - paralelismo;
		- Uso do UDP no lugar de TCP (HTTP/3).
- 2 a 8 conexões TCP por domínio, dependendo do navegador.

> [!tldr]+ Otimizações para o conteúdo *web* com HTTP 1.1
> - Reduzir chamadas DNS;
> - Diminuir o número de requisições;
> - Rede de Distribuição de Conteúdo (CDN);
> - Melhorar o cache com cabeçalhos `expire` e `etag`;
> - Negociar compactação de dados (gzip);
> - Evitar redirecionamentos;
> - Usar vários domínios para aumentar o número de conexões TCP;
> - Concatenação de objetos (JS, CSS), *spriting* de imagens;
> - *Inlining* (embutir *scripts* e outros conteúdos nas páginas HTML).

## HTTP/2.0

- Criado com o objetivo de melhorar o carregamento de páginas, reduzir a latência de aplicações *web* e eliminar a necessidade de otimizações *ad hoc* das aplicações.
- Maio de 2015, baseado em seu precursor SPDY.
- Não requer modificações nas aplicações.
- Aplicações usam recursos do HTTP/2 para melhorar a eficiência.
- **Compatibilidade entre HTTP 1.1 e HTTP/2**.
	- Cabeçalho HTTP `upgrade`.
	- TLS.
		- SPDY: *Next Protocol Negotiation* (NPN).
		- HTTP/2: *Application Layer Protocol Negotiation* (ALPN).
	- Os principais clientes *web* não implementam HTTP/2 sem TLS.
- Multiplexação da conexão TCP, várias requisições e respostas enviadas de forma concorrente no mesmo canal.
- **Enquadramento binário.** Divide e encapsula as mensagens em quadros (protocolos de texto vs. binários).
- Frame HTTP/2: cabeçalho de *frame* e dados.
- Cabeçalhos compactados, compressão de dados, atribuição de prioridades (pesos e precedências).
- *Push* do servidor: armazenados em cache pelo cliente, reutilizados em diferentes páginas, multiplexados junto com outros recursos, priorizados pelo servidor.
	- Reduz a necessidade de otimizações *ad hoc*.
	- Diminui a carga nos servidores e clientes.

## QUIC e HTTP/3

- **QUIC.** Protocolo confiável e seguro que pode ser usado para transportar dados de protocolos de aplicação e que resolve os problemas de TCP nos protocolos HTTP1.1 e HTTP/2.
	- Cria múltiplos fluxos da conexão IP.
	- Orientado a *bytes*, suporta a mobilidade (troca de endereço IP).
	- É sempre seguro (criptografado).
	- Abertura rápida de conexão 1-RTT e 0-RTT.
	- Desenvolvido pelo Google.
- Servidores *web* - NGINX (maio/2023).
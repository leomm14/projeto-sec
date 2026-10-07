# Demo: HTTP Headers Scanner

## 1. Visão Geral do Projeto
O **HTTP Headers Scanner** é uma ferramenta de linha de comando desenvolvida em Python para analisar as respostas HTTP de aplicações web e identificar a ausência ou má configuração de cabeçalhos de segurança cruciais. Ao auditar regras de segurança, a ferramenta gera um relatório visual informando possíveis vulnerabilidades que podem abrir brechas para ataques como Clickjacking, XSS, SSL Stripping e vazamento de referer.

## 2. Resultados e Decisões Técnicas

### Funcionalidade e Compreensão
A ferramenta executa com sucesso o rastreio em URLs alvo, processando os dicionários de `response_headers` e classificando o nível de segurança do site de A a F.
Para expandir o escopo original, a ferramenta foi aprimorada com a adição de um sétimo cabeçalho de segurança: **Cross-Origin-Opener-Policy (COOP)**. O script agora exige e recomenda a configuração `same-origin`, que é essencial na web moderna para isolar a página contra interações maliciosas de outras origens via `window.opener`, mitigando riscos de ataques *cross-origin*.

### Validação e Testes
Com a inserção da nova regra do COOP (severidade média), o total de pontos possíveis de avaliação do sistema subiu de 100 para 115. Isso revelou uma falha de escalabilidade na suíte original de testes, que dependia de limites de pontuação absolutos cravados (como 90 e 83 pontos). 
A principal **decisão técnica** foi refatorar o `test_http_headers_scanner.py` para calcular os `thresholds` (limites de nota A e B) de forma 100% dinâmica através de porcentagem (ex: 85% do total atual de pontos). Os testes agora geram distribuições de falhas e acertos dinamicamente baseados na constante `RULES`, garantindo que a aplicação esteja completamente preparada para a adição de dezenas de novas verificações no futuro sem quebrar a validação. O comando `just test` passa com sucesso em 100% dos casos.

### Segurança
O sistema adota o padrão de "núcleo funcional, casca imperativa". A validação das regras não depende de rede e é testada de forma isolada. Os testes de integração da camada de rede utilizam `respx` para mockar respostas HTTP localmente, evitando *requests* desnecessários. Além disso, as requisições ao vivo configuram um `User-Agent` customizado e claro, respeitando a identificação em infraestruturas e servidores alheios.

---

## 3. Demonstração Prática (Vídeo)

No vídeo abaixo, detalho o funcionamento, executo o scanner ao vivo em uma aplicação real para visualizar o comportamento do novo cabeçalho COOP e mostro a refatoração efetuada nos testes unitários.

🎥 **Link do Vídeo:** [https://youtu.be/K7Yk0SsYgKc]
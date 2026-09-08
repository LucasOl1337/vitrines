# OL.GG — notas da vitrine

Verificação: 7 de setembro de 2026.

OL.GG é uma interface interna de consulta ao workspace de Lucas Oliveira. Esta página apresenta gravações reais da interface com uma fixture fictícia. Atlas Notes, Prisma Studio, Órbita, Onda Audio, Farol UI e Pulso são exemplos da demonstração; não representam projetos ou resultados profissionais reais de Lucas.

## Funcionalidades verificadas

- 5 áreas principais: Visão geral, Projetos, Atividade, Contexto e Ferramentas.
- 11 telas verificadas em 1600 × 1000 e 390 × 844 pixels: visão geral, catálogo, detalhe de projeto, atividade, cinco grupos de contexto, harnesses e stack.
- Busca normaliza maiúsculas e acentos e procura em nome, descrição, tecnologias, estado detalhado e próxima ação.
- Filtros por categoria e estado, ordenação por nome, estado vazio e limpeza dos filtros.
- URL preserva busca, filtros e projeto aberto; retorno do detalhe, recarga e histórico do navegador foram verificados.
- Contexto com grupos e blocos recolhíveis. Dados internos reais permanecem no snapshot local.
- Horas ausentes não viram zero. Span representa intervalo observado, não tempo produtivo. Somas podem incluir sobreposição de sessões.
- Rótulos active/paused do snapshot são interpretados sem apagar a indicação de produção do catálogo.
- Harnesses no mobile usam cartões; catálogo passa a uma coluna.

- Vídeo final de 75,03 segundos, gravado em bancada isolada, com narração corrigida do OmniVoice e 20 cues de legenda em português. Versão web em 1280 × 720, H.264 e áudio AAC estéreo 48 kHz.

## Origem dos fatos

A comparação entre produtos descreve responsabilidades encontradas nos READMEs locais de `Daily Work app/README.md`, `OL.GG/README.md` e `LucasOL site/README.md`, além das interfaces revisadas nesta data. DailyWork organiza os dias e os cards; OL.GG consulta contexto e registros internos; LucasOL apresenta um portfólio público. Não é uma comparação de desempenho com concorrentes.

As contagens 5 áreas, 11 telas, 390 pixels e 6 projetos de demonstração vêm da interface e da verificação registrada nesta revisão. Nenhuma contagem da fixture constitui medição profissional de Lucas Oliveira.

## Uso local

No checkout autorizado, entre no diretório OL.GG e execute `npm ci`, seguido de `npm run dev`. Para usar apenas os dados fictícios, execute `npm run dev -- --mode demo`. O servidor padrão atende em `http://localhost:4546`.

A vitrine não oferece um binário público ou o snapshot pessoal para download. Gravação, imagens e fixture foram preparadas separadamente dos dados privados.

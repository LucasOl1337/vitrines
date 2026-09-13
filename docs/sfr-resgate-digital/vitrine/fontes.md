# Fontes da vitrine SFR Resgate Digital

Conferência em 7 de setembro de 2026. Revisão de referência do checkout: `575ea7ea`. Os caminhos abaixo são relativos à raiz do repositório e constituem referências de auditoria; não são rotas do site.

## Identidade e regras

| Afirmação ou decisão | Fonte local | Evidência |
|---|---|---|
| App para atendimento em campo e painel para revisão, sincronização e entrega | `README.md` | Descrição inicial do produto. |
| A prioridade é a agilidade do operador em campo | `GLOBAL_TRUTH.md`, regras 1 e 6 | Regra de produto, sem medição de ganho de tempo. |
| Paleta navy, azul e amarelo | `apps/web/src/app/tokens.css` | `--navy-900: #000640`, `--navy-500: #3828D8`, `--gold-500: #F6C92E`. |
| Fonte Encode Sans Expanded | `apps/web/src/app/tokens.css` | Família tipográfica declarada nos tokens; a landing usa Google Fonts. |
| Geometria da marca | `apps/web/src/components/logo.tsx` | Cinco retângulos, com rotações de ±45°, mesma geometria oficial e cor do token. |

## Capacidades apresentadas

| Afirmação na página ou no roteiro | Fonte local | Limite do que está afirmado |
|---|---|---|
| Indicadores de ocorrências, vítimas, tempo de resposta, saída da base e pendências de sincronização | `apps/web/src/app/dashboard/page.tsx` | Contagem de cinco cartões `KpiCard`, não benchmark e não número de atendimentos reais. |
| Filtros por período, concessionária e base; últimas ocorrências | `apps/web/src/app/dashboard/page.tsx` | Controles e tabela existentes no painel. |
| Consulta de ocorrências | `apps/web/src/app/ocorrencias/page.tsx` | Lista real do produto. |
| Equipe, viatura e local associados à ocorrência | `apps/web/src/app/ocorrencias/[id]/page.tsx` | Cartão de dados da ocorrência e rótulos operacionais. |
| Fichas individuais por vítima, múltiplas vítimas por ocorrência | `apps/web/src/app/ocorrencias/[id]/page.tsx`; `apps/mobile/src/fichas-de-vitimas/multi-victim-schema.ts` | A página apresenta vítimas da ocorrência; o modelo mobile tem entrada por vítima. |
| Prontuário em PDF | `apps/web/src/app/ocorrencias/[id]/page.tsx`; `apps/web/src/entrega-de-documentos/occurrence-pdf.ts` | Documento com dados da ocorrência e fichas. A narração diz “pode ser gerado”. |
| Acompanhamento de entregas | `apps/web/src/app/ocorrencias/[id]/page.tsx`; `apps/web/src/entrega-de-documentos/process-deliveries.ts` | Painel e processamento existem. Não declarar envio externo concluído durante a gravação. |
| Participantes operacionais com função e situação | `apps/web/src/app/configuracoes/participantes/page.tsx` | Cadastro de participantes, com papel e situação ativa/inativa. |
| Cadastro de frota | `apps/web/src/app/viaturas/page.tsx` | Tela “Frota de viaturas”, com acesso ao cadastro. |
| Indicadores com filtros de período, concessionária e base | `apps/web/src/app/indicadores/page.tsx` | Filtros e construção da visão operacional presentes na página. |
| Pessoas e acessos do painel, com papel e situação | `apps/web/src/app/usuarios/page.tsx` | Tela de usuários do painel; distinta dos participantes operacionais que compõem a equipe em campo. |
| Relatórios consolidados por concessionária e período, com exportações | `apps/web/src/app/relatorios/page.tsx` | Página real de relatórios; não usamos números do cenário de demonstração como desempenho operacional. |
| Eventos de auditoria consultáveis com filtros e permissão | `apps/web/src/app/configuracoes/auditoria/page.tsx` | A rota exige `users:manage` e usa `listAuditEvents`. Não fazemos afirmação jurídica ou de certificação. |
| App com registro local e fila de sincronização | `README.md`; `apps/mobile/src/sincronizacao/aparelho-para-painel/auto-sync.ts`; `apps/mobile/src/lib/local-schema.ts` | Capacidade encontrada no código. A captura do painel não demonstra armazenamento nativo nem ciclo offline real. |
| Conexão inicial para assumir viatura e liberar contexto offline | `apps/mobile/src/acesso-da-ambulancia/offline-session-policy.ts` | `shouldAllowLocalOccurrence` exige contexto operacional e explica a preparação inicial com internet. Não promete uso offline incondicional. |
| Pedido de cópia no fluxo de atendimento | `apps/mobile/src/entrega-de-documentos/occurrence-pdf-queue.ts` | Fila de pedido de documentos no app. |
| Instalação e acesso orientados pela organização | `apps/mobile/app.json`; `apps/web/src/lib/requireSession.ts`; `apps/mobile/src/acesso-da-ambulancia/offline-session-policy.ts` | App instalado e contextos autenticados. A landing não oferece distribuição pública, cadastro público nem endereço de contato inventado. |

## Natureza da comparação

A tabela compara duas partes do próprio SFR: app em campo e painel de gestão. Não é comparação com concorrentes. Todas as capacidades foram conferidas no código em 07/09/2026. Nenhuma alegação de superioridade, economia de tempo, disponibilidade ou desempenho foi adicionada.

## Capturas do app

As telas `app-campo.png`, `app-ocorrencia.png` e `app-ficha.png` mostram o app real na versão web, executado em instância local. Fontes de navegação: `apps/mobile/src/app/index.tsx`, `apps/mobile/src/app/nova-ocorrencia.tsx` e `apps/mobile/src/app/ficha/[victimId].tsx`. A página descreve somente os elementos visíveis: início da ocorrência, atendimentos em andamento, estado de sincronização, marcos de tempo, local e acesso à ficha. Essas capturas não comprovam funções nativas de hardware.

## Medições e mídia

- **Cinco indicadores:** contagem dos cinco cartões no código do dashboard.
- **Doze telas:** três capturas da versão web do app e nove capturas do painel presentes em `docs/media/`: painel, ocorrências, ficha, pessoas e acessos, viaturas, auditoria, relatórios, indicadores e participantes operacionais. O arquivo `equipes.png` mostra Pessoas e acessos; `configuracoes.png` mostra Participantes operacionais. As imagens do painel medem 1600 × 1000 pixels; a tela inicial e a criação de ocorrência do app medem 430 × 900 pixels; a ficha mede 430 × 932 pixels.
- **Vídeo web medido:** `docs/media/sfr-promo-720p.mp4`, 1280 × 720 pixels, H.264 a 30 quadros por segundo, áudio AAC estéreo a 48 kHz. Duração de 75,033333 segundos e tamanho de 1.686.426 bytes. Medição com `ffprobe` em 13/09/2026; a página arredonda a duração para 75 s (75,03 s no texto).
- **Vídeo 1080p medido:** artefato de entrega separado da versão web, em `~/Videos/sfr-resgate-digital-promo-1080p.mp4`: 1920 × 1080 pixels, H.264 a 30 quadros por segundo, AAC estéreo a 48 kHz, 75,033333 segundos e 3.563.804 bytes. Medido com `ffprobe` em 13/09/2026.
- Capturas e gravações vieram de uma instância local isolada, com dados fictícios. O PDF mostrado foi exportado pelo produto e a tela de auditoria registra a exportação local. Não houve envio de e-mail externo.
- `docs/operacao.md` explicita que o export web não comprova câmera, áudio, geolocalização, SQLite nativo ou SQLCipher. A página preserva esse limite de evidência.

## Verificação da página

A página foi renderizada e inspecionada com Chromium headless em perfis isolados, nas larguras de 1440 e 390 pixels. As doze imagens foram decodificadas nas dimensões registradas acima; não houve transbordamento horizontal. O HTML tem um único conteúdo principal e um único título principal, com identificadores sem duplicação. Todas as referências locais de `index.html` e `narracao.html` apontam para arquivos existentes.

A reprodução foi verificada por HTTP em um servidor local temporário: metadados 1280 × 720 e 75,033333 s, nenhum erro do elemento de vídeo, avanço de reprodução confirmado. As quatorze entradas de legenda carregaram; ao buscar 23 s, a legenda exibida correspondeu à narração sobre viatura, horários e local. O último trecho termina em 73,60 s, dentro da duração do vídeo. Nenhum recurso local da página retornou erro HTTP durante essas verificações. O navegador e o servidor de verificação foram encerrados ao concluir.

## Fora de escopo

As mídias desta vitrine não contêm dados reais de atendimento. Não houve afirmação de envio de e-mail concluído, teste nativo de hardware, desempenho de produção, conformidade legal, certificação, percentual de redução de tempo ou disponibilidade. Publicação, commit, push e release dependem de ordem explícita do dono, conforme o contrato do repositório.


## Atualização da narração

Em 07/09/2026, as sete falas foram substituídas pelos WAVs selecionados na revisão de voz. A faixa PCM mantém integralmente as amostras dos arquivos selecionados, posicionadas conforme o roteiro, sem ajuste adicional de ganho, normalização, fade de áudio ou velocidade. O vídeo conserva os mesmos quadros; os hashes dos streams de vídeo anterior e atualizado são iguais. As legendas foram ajustadas às novas falas; último trecho até 73,60 s. A duração permanece 75,033333 s. Decodificação integral das duas resoluções concluída sem erro.

Em 13/09/2026, as sete falas foram regeradas no OmniVoice local com o perfil de voz `04b04ba5` ("Lucas Oliveira — OFICIAL #1 · Studio Clean v2"), resolvido por `GET /api/mcp/default` e confirmado em `/v1/audio/voices` no momento da geração. As narrações anteriores não registravam o ID do perfil usado e foram substituídas por essa razão. O vídeo foi remontado com as mesmas cenas e slides; duração medida de 75,033333 s. As legendas foram regeneradas a partir dos segmentos transcritos pelo próprio OmniVoice; último trecho termina em 74,59 s. Manifesto com hashes: `output/vitrine-producao/revoz-04b04ba5/audio/audio-manifest.json` (diretório local, fora do versionamento).

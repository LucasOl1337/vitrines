# Inventário operacional do aplicativo móvel

Leitura do código em 2026-09-07, checkout com HEAD `575ea7ea` e alterações locais preexistentes. Este inventário descreve a interface implementada; não é certificado de que cada recurso foi executado. O roteiro correspondente é `roteiro-mobile.json`. A gravação precisa registrar separadamente o que foi demonstrado, o que falhou e o que continua pendente.

Escopo: aplicativo de campo, acesso da ambulância, ocorrência, fichas, mídia, documentos, sincronização e manutenção do aparelho. O painel administrativo tem inventário próprio. Usar somente cenário identificado como demonstração, pessoas fictícias e e-mails `example.invalid`. A seleção de avaliações, procedimentos, escalas ou desfechos reproduz dados previamente definidos pelo instrutor; este material não ensina decisões clínicas.

## Telas e superfícies

| Entrada | Tela e superfícies subordinadas |
|---|---|
| `/login` | Código da ambulância, senha, mostrar/ocultar, erros de acesso. |
| `/escolher-viatura` | Escolha de viatura após autenticação de superadministrador; não é o fluxo normal da equipe. |
| `/` | Início, contexto operacional, atalhos, ocorrência de destino para nova vítima, resumo da sincronização. |
| `/ocorrencias` | Ativas/Finalizadas; busca e período; acesso ao detalhe e a conflitos. |
| `/nova-ocorrencia` | Cliente quando aplicável, marcos de tempo, nome, rodovia/KM, QTH. |
| `/vitimas?occurrenceId=…` | Dados, Equipe, Mídia, lista de fichas, criação de vítima, conclusão, PDF, envio e histórico de cópias. |
| `/equipe-ocorrencia/[occurrenceId]` | Lista, identificação, função/conselho, assinatura e definição de responsável. |
| `/ficha/[victimId]` | Identificação, índice/roteiro, blocos FAR, gravadores, documentos, revisão, desfecho, anulação/correção. |
| `/captura-rapida` | Rascunhos soltos de áudio e foto; reprodução, nome, horário e tela cheia. |
| `/config` | Identidade da viatura, voz como rascunho, saída e acesso avançado. |
| `/config-avancado` | Detalhes do aparelho, segurança, evidência, backup e versão. |

São **11 rotas de tela**. `_layout.tsx` monta sessão, bloqueio de versão, navegação e folhas globais; não é uma décima segunda tela operacional. Modais transversais: Sync, reautenticação, Resolver conflito, confirmação, aviso, teclado, seletores, assinatura e visualização de foto. A barra fixa contém **Início, Ocorrências, Nova, Sync e Config**.

## Catálogo de cobertura

Cada ID é uma unidade de cobertura. Uma linha que contém alternativas só estará demonstrada por completo quando os ramos indicados forem registrados; mostrar o primeiro item não comprova os demais. Os caminhos abaixo são relativos ao repositório.

### Acesso e navegação

| ID | Controles, resultados e condições | Fonte principal |
|---|---|---|
| M-ACESSO-01 | `Código da ambulância`, `Senha`, `Mostrar senha`/`Ocultar senha`, `Entrar`; carregamento e erro. Primeiro acesso precisa obter contexto operacional online. | `apps/mobile/src/app/login.tsx` |
| M-ACESSO-02 | Superadministrador: `Escolher viatura`, lista de viaturas ativas, entrar como a escolhida, atualização/ausência de viaturas e voltar. | `apps/mobile/src/app/escolher-viatura.tsx` |
| M-ACESSO-03 | Retomar sessão provisionada offline; falha de rede não equivale a bloqueio. Sessão recusada exige confirmação; bloqueio da base impede nova ocorrência. | `apps/mobile/src/acesso-da-ambulancia/offline-session-policy.ts`, `apps/mobile/src/app/_layout.tsx` |
| M-ACESSO-04 | Aviso `Atualização pronta — reiniciar`, `Reiniciando…`; versão mínima abre `Atualize o aplicativo`, versões instalada/necessária e `Baixar versão nova` quando há URL. | `apps/mobile/src/atualizacao-do-aplicativo/app-update-banner.tsx`, `apps/mobile/src/atualizacao-do-aplicativo/app-blocked-screen.tsx` |
| M-NAV-01 | Barra Início/Ocorrências/Nova/Sync/Config; voltar e fechar folhas. Identificar viatura/base antes de escrever. | `apps/mobile/src/components/bottom-nav.tsx`, `apps/mobile/src/app/index.tsx` |
| M-NAV-02 | Início: `Nova ocorrência`, `Registrar agora`, `Rascunho rápido`, `Áudio ou foto agora`, `Nova vítima`; escolher ocorrência de destino em `Trocar` quando há mais de uma. | `apps/mobile/src/app/index.tsx` |
| M-NAV-03 | Ocorrências: `Ativas`/`Finalizadas`, `Buscar ocorrência`/`Fechar busca e filtro`; busca por nome, vítima, protocolo do cliente, número, KM, rodovia e mecanismo; períodos `7 dias`, `30 dias`, `Tudo`; contagens e estados vazios. | `apps/mobile/src/app/ocorrencias.tsx`, `apps/mobile/src/ocorrencias/occurrence-search-view.ts` |
| M-NAV-04 | Vítimas: `Ativas`, `Anuladas`, `Reconciliadas`; abrir ficha pelo número; filtro de gravidade aparece a partir de cinco fichas ativas, `Todas` limpa filtro; paginação, carregamento e `Tentar novamente`. | `apps/mobile/src/ocorrencias/victim-roster.tsx`, `apps/mobile/src/ocorrencias/occurrence-panel-buttons-view.ts` |

### Ocorrência e participantes

| ID | Controles, resultados e condições | Fonte principal |
|---|---|---|
| M-OCOR-01 | Nova ocorrência herda viatura/base/concessionária; escolher cliente somente quando catálogo permite. Falta de vínculo/contexto/cliente mostra motivo e bloqueia criação. | `apps/mobile/src/app/nova-ocorrencia.tsx`, `apps/mobile/src/ocorrencias/operational-context.ts` |
| M-OCOR-02 | Marcos `ACIONADO`, `SAÍ DA BASE`, `CHEGUEI NO LOCAL`: digitar hora válida ou `AGORA`; proveniência diferencia hora informada e capturada. Nome opcional distinto do número automático. | `apps/mobile/src/app/nova-ocorrencia.tsx`, `apps/mobile/src/ocorrencias/occurrence-open-summary.ts` |
| M-OCOR-03 | Rodovia do contexto fica fixa; se não definida, tipo/rodovia são informados. KM obrigatório. QTH `Capturar`, `Buscando…`, `Atualizar` ou `Tentar`; precisão quando disponível; ausência de GPS não impede atendimento. `INICIAR ATENDIMENTO` valida e abre criação de vítima. | `apps/mobile/src/app/nova-ocorrencia.tsx`, `apps/mobile/src/ocorrencias/occurrence-location.ts` |
| M-OCOR-04 | `Dados` → `Dados da ocorrência`: rodovia, KM, acionamento, saída e chegada; números de veículos e pessoas; cena: PP, Incêndio, Preso em ferragem + minutos, Encarcerado, Ribanceira, Deambulando, Clínico, Outro. Valores incompletos ficam em edição; erro explica a entrada inválida. | `apps/mobile/src/ocorrencias/occurrence-detail-fields.ts`, `apps/mobile/src/ocorrencias/occurrence-detail-section.tsx` |
| M-OCOR-05 | Dados: mecanismos Colisão frontal, Lateral D/E, Traseira, Capotamento/tombamento, Múltiplas, Saída de pista, Atropelado; impactos Frente/Traseira/Lateral veíc., Mureta, Árvore, Barranco, Pedestre, Animal. | `packages/regras-de-resgate/src/operational-occurrence.ts`, `apps/mobile/src/ocorrencias/occurrence-detail-section.tsx` |
| M-OCOR-06 | Dados: protocolo do cliente; `Enviar cópia ao cliente na sincronização` quando política sob demanda permite; hospital, outro destino, caso passado para, horário no hospital/término/SAU-BSO; vincular/desvincular ambulâncias de apoio sem repetir principal. | `apps/mobile/src/ocorrencias/occurrence-detail-fields.ts`, `apps/mobile/src/ocorrencias/occurrence-detail-section.tsx` |
| M-OCOR-07 | `Apagar ocorrência` pede confirmação explícita Manter/Apagar ocorrência e apaga a cópia local com fichas e mídias associadas. Não é anulação clínica nem exclusão do servidor; cópia remota pode reaparecer em atualização. | `apps/mobile/src/ocorrencias/local-occurrence-delete.ts`, `apps/mobile/src/app/vitimas.tsx` |
| M-EQUIPE-01 | `Equipe` → `Participantes` → editar participantes. Lista, adicionar, buscar nome/sugestão existente ou `Usar como novo participante`, editar e remover explicitamente. | `apps/mobile/src/ocorrencias/occurrence-team-screen.tsx`, `apps/mobile/src/ocorrencias/occurrence-team-panel.tsx` |
| M-EQUIPE-02 | Nome obrigatório; Função: Sem função, Médico, Enfermeiro, Técnico de enfermagem, Socorrista. Registro e Conselho: Sem conselho, CRM, COREN, CREFITO, CRN, CRO, CRF, Outro conselho. Validar par conselho/registro; funções médico/enfermeiro exigem registro pela implementação. | `apps/mobile/src/ocorrencias/participant-editor-fields.tsx`, `apps/mobile/src/ocorrencias/participant-choice-sheets.tsx` |
| M-EQUIPE-03 | `Avançar` para assinatura; adicionar/salvar alterações; coletar/refazer assinatura. Alterar função assinada abre `Invalidar assinatura?` → `Salvar e invalidar`. | `apps/mobile/src/ocorrencias/occurrence-team-screen.tsx`, `apps/mobile/src/fechamento-do-atendimento/signature-pad.tsx` |
| M-EQUIPE-04 | Escolher/adicionar responsável; assinatura e responsabilidade são salvas juntas em `Salvar responsável`. Política da operação: somente responsável/toda equipe/opcional; faltas necessárias explicam bloqueio de fechamento. | `apps/mobile/src/ocorrencias/occurrence-responsible-entry.tsx`, `apps/mobile/src/ocorrencias/occurrence-team-screen.tsx` |

### Mídia, PDF e entregas

| ID | Controles, resultados e condições | Fonte principal |
|---|---|---|
| M-MIDIA-01 | `Rascunho rápido`: `Gravar áudio`, `Parar`, salvamento; `Tirar foto agora`; `Soltos neste aparelho`. Não associa automaticamente a ocorrência. | `apps/mobile/src/app/captura-rapida.tsx` |
| M-MIDIA-02 | Rascunho solto: editar nome com conclusão/desfoque para salvar; editar horário por Hora/AGORA; reproduzir/pausar áudio; abrir/fechar foto em tela cheia. | `apps/mobile/src/midias-da-ocorrencia/loose-draft-row.tsx`, `apps/mobile/src/midias-da-ocorrencia/loose-audio-play-control.tsx` |
| M-MIDIA-03 | `Mídia` → `Áudios e fotos`: gravador da ocorrência; `Anexar rascunho já gravado`; foto solta pede classificação Cena ou veículo. Conferir ocorrência/ficha de destino antes de anexar. | `apps/mobile/src/app/vitimas.tsx`, `apps/mobile/src/midias-da-ocorrencia/loose-draft-attach-sheet.tsx` |
| M-MIDIA-04 | Fotografar cena/veículo, visualizar foto e estado de upload, `Tentar reenviar esta foto`. Máximo 12 anexos de cena+veículo; ocorrência encerrada/somente leitura não aceita novas fotos. | `apps/mobile/src/app/vitimas.tsx`, `apps/mobile/src/midias-da-ocorrencia/occurrence-photo-capture.ts` |
| M-MIDIA-05 | Áudio local entra na fila; estados Rascunho/Aguardando envio/Pendente transcrição/Pendente revisão/Revisado e erros IA não configurada/Erro na transcrição/Sem fala reconhecível/Não publicado. Não confundir arquivo salvo com transcrição publicada. | `apps/mobile/src/midias-da-ocorrencia/voice-draft-review.ts`, `apps/mobile/src/midias-da-ocorrencia/voice-input.tsx` |
| M-MIDIA-06 | Lista de rascunhos: expandir/recolher, reproduzir/pausar, posição do áudio, transcrição completa/recolher e `Revisar` quando suportado; revisão de alguns rascunhos gerais ocorre na base. Áudio só na base é informado; descarte só aparece quando indisponível local e remoto, com Manter/Descartar. | `apps/mobile/src/midias-da-ocorrencia/voice-draft-list.tsx`, `apps/mobile/src/midias-da-ocorrencia/voice-draft-review.ts` |
| M-MIDIA-07 | Aviso de integridade de foto: verificar novamente e `Marcar aviso como revisado`; marcar aviso não recupera arquivo perdido. | `apps/mobile/src/midias-da-ocorrencia/durable-photo-integrity-panel.tsx` |
| M-MIDIA-08 | Guia do gravador geral na Identificação: `Fale corrido, nesta ordem:`, lista de campos, exemplo e estados `já preenchido`/`preenchido agora`; resumo de aplicação distingue campos aplicados e ignorados. O guia orienta a fala, não comprova que houve reconhecimento. | `apps/mobile/src/fichas-de-vitimas/audio-fill-guide.tsx`, `packages/regras-de-resgate/src/ficha-audio-fill.ts` |
| M-ENTREGA-01 | Gerar/abrir PDF: `Atual`, `Precisa sincronizar`, `Indisponível`. Exige conexão, sessão válida e fila confirmada; estado pendente abre Sync; PDF usa dados confirmados no servidor. | `apps/mobile/src/entrega-de-documentos/occurrence-pdf-state.ts`, `apps/mobile/src/app/vitimas.tsx` |
| M-ENTREGA-02 | `Enviar cópia da ficha`: um `E-mail do hospital`, responsável da equipe e uma ação `Enviar cópia`; validação indica o que falta. A solicitação entra na fila; não é comprovante de entrega. | `apps/mobile/src/entrega-de-documentos/request-sheet.tsx` |
| M-ENTREGA-03 | Histórico de envio: Aguardando sincronização, Aceito pelo provedor, Entregue, Requer atenção, Cancelado; solicitado/tentativas/aceito/entregue/baixado/link expirado/revogado; `Cancelar` enquanto elegível, `Enviar novamente` nas condições de falha/expiração/cancelamento. | `apps/mobile/src/entrega-de-documentos/victim-delivery-history.tsx`, `apps/mobile/src/entrega-de-documentos/request-status.ts` |

### Sincronização, conflitos e aparelho

| ID | Controles, resultados e condições | Fonte principal |
|---|---|---|
| M-SYNC-01 | Sync global ou desta ocorrência: conexão, fila, andamento e itens com nome/gravidade/estado/erro; `Enviar agora`, bloqueio por falta de sinal, reenvio automático transitório. Salvo localmente não significa confirmado na base. | `apps/mobile/src/sincronizacao/acompanhamento/sync-status-sheet.tsx` |
| M-SYNC-02 | `Buscar agora` atualiza lista remota; `Conferir com o servidor` compara ocorrência; dados locais pendentes são enviados antes da incorporação de atualização remota. Número confirmado pelo servidor pode gerar aviso de renumeração. | `apps/mobile/src/sincronizacao/acompanhamento/sync-status-sheet.tsx`, `apps/mobile/src/sincronizacao/painel-para-aparelho/refresh.ts`, `apps/mobile/src/app/vitimas.tsx` |
| M-SYNC-03 | `Entrar de novo`/reautenticação dentro de Sync: Código da ambulância e Senha; deve corresponder à ambulância dos registros existentes. Bloqueio da base exibe motivo; não limpar dados para contornar. | `apps/mobile/src/sincronizacao/acompanhamento/reauth-view.ts`, `apps/mobile/src/sincronizacao/autenticacao/inline-reauthentication.ts` |
| M-SYNC-04 | Reenviar fotos com falha e consultar bloqueio específico; ocorrência recusada pode oferecer exclusão apenas local com confirmação. | `apps/mobile/src/sincronizacao/acompanhamento/sync-status-sheet.tsx` |
| M-SYNC-05 | `Resolver`: comparar aparelho/servidor e campos/coleções divergentes. `Aceitar o do servidor` → confirmação `Aceitar servidor`; `Manter o deste aparelho` → `Manter e reenviar`. Escolha do aparelho indisponível se servidor encerrado ou sem mutação reenviável; nenhuma escolha é recomendada sem conferir conteúdo. Diferenças apenas técnicas se reconciliam automaticamente. | `apps/mobile/src/sincronizacao/conflitos/resolution-sheet.tsx`, `apps/mobile/src/sincronizacao/conflitos/view.ts`, `docs/contrato-de-conflitos.md` |
| M-CONFIG-01 | Configurações mostra Viatura, Código da ambulância, Tipo, Base/trecho; Voz como rascunho e gravação local; acesso a Avançado e versão. | `apps/mobile/src/app/config.tsx`, `apps/mobile/src/lib/mobile-config-view.ts` |
| M-CONFIG-02 | Avançado: Detalhes do aparelho (rodovia, concessionária, cliente, envio e política de assinatura); Segurança e LGPD são informações de estado, não certificado. Expandir/recolher seções. | `apps/mobile/src/app/config-avancado.tsx`, `apps/mobile/src/lib/config-sections-view.ts` |
| M-CONFIG-03 | Evidência do teste físico: dados da última ocorrência, `Atualizar evidência` e `Compartilhar evidência do aparelho`. Exibir esse resumo não comprova sozinho um teste físico executado. | `apps/mobile/src/app/config-avancado.tsx` |
| M-CONFIG-04 | Backup criptografado: Senha do arquivo com mínimo 8 caracteres; `Exportar backup criptografado`, `Compartilhar backup`, `Validar backup`; validação confere arquivo/senha e contagens, não restaura dados. | `apps/mobile/src/app/config-avancado.tsx` |
| M-CONFIG-05 | `Sair da conta`: tenta enviar quando possível, gera backup local, limpa dados locais da conta e encerra sessão; falha de rede não impede saída. Não ensinar como forma de preservar rascunhos visíveis. Backup automático de logout não é o mesmo arquivo criptografado exportado em Avançado. | `apps/mobile/src/acesso-da-ambulancia/account-logout.ts`, `apps/mobile/src/app/config.tsx` |

### Ficha: criação, índice e identificação

| ID | Controles, resultados e condições | Fonte principal |
|---|---|---|
| M-FICHA-01 | Assistente: `Identifique a vítima` → `CONTINUAR PARA GRAVIDADE` → `Agora, a gravidade` → `Monte o roteiro da ficha`. Nome ou Não identificado, Idade/Idade estimada, Sexo (inclui Não determinado), `MARCAR INÍCIO` opcional. Voltar à identificação/Alterar gravidade. | `apps/mobile/src/ocorrencias/victim-creator-sheet.tsx` |
| M-FICHA-02 | Roteiro seleciona seis grupos: Vítima; Avaliação primária; Sinais e escalas; Lesões e queimaduras; Evolução e medicação; Desfecho. `ABRIR ROTEIRO`, `ABRIR ÍNDICE`, `ABRIR BONECO`. Zero grupos abre índice completo; retirar grupo da sequência não apaga dados. | `apps/mobile/src/fichas-de-vitimas/ficha-route-selector.tsx`, `apps/mobile/src/fichas-de-vitimas/ficha-route-plan.ts` |
| M-FICHA-03 | Índice `Escolher o que preencher agora`, `Partes da ficha`, preenchidos/total. Doze passos: Identificação, Documento, Triagem, Equipe médica, XABCDE, Cinemática, Sinais vitais, Escalas, Lesões e queimaduras, Observações, Medicação, Desfecho. Barra de passos, concluir seção/próximo, concluir roteiro/voltar ao índice, `SALVAR E SAIR` aguarda gravações pendentes. | `apps/mobile/src/fichas-de-vitimas/ficha-navigation-view.ts`, `apps/mobile/src/app/ficha/[victimId].tsx` |
| M-FICHA-04 | Identificação: nome, `MARCAR COMO NÃO IDENTIFICADO`, idade/estimada, Masculino/Feminino/Não determinado; endereço rua/avenida/nº/bairro/CEP/cidade/UF, telefone, escolaridade; início da anotação e `Preencher com áudio`. Cronômetro no bloco tem limitação registrada abaixo. | `apps/mobile/src/fichas-de-vitimas/vitima-block.tsx`, `packages/regras-de-resgate/src/far.ts` |
| M-FICHA-05 | CEP completo ao sair do campo: `Consultando CEP…`, `Sugestão do CEP`; `Confirmar` preenche somente vazios, `Recusar` descarta, `Fechar` quando nada aplicável. Erro permite tentar novamente; endereço manual continua disponível. | `apps/mobile/src/fichas-de-vitimas/cep-address-lookup.tsx` |
| M-FICHA-06 | Documento: Frente/Verso, fotografar/refotografar/remover cada lado; permissão e falhas de câmera/persistência; foto reduzida e salva antes de vincular. | `apps/mobile/src/fichas-de-vitimas/vitima-block.tsx`, `apps/mobile/src/midias-da-ocorrencia/document-photo-capture.ts` |
| M-FICHA-07 | RG, CPF, tempo de habilitação; placa, tipo Passeio/Utilitário/Ônibus/Moto/Caminhão/Outro, UF, modelo/cor. Danos Deformidade, volante, cabine (>40cm), parabrisa, painel, capacete. Máscara de CPF e normalização de placa. | `apps/mobile/src/fichas-de-vitimas/vitima-block.tsx`, `packages/regras-de-resgate/src/far.ts` |
| M-FICHA-08 | Campo preenchido por IA: `Preenchido pela IA · revisar` + `Confirmar`; edição manual também registra revisão. Não existe aceitar todos os campos OCR nessa rota. | `apps/mobile/src/fichas-de-vitimas/ai-pending-review-field.tsx`, `apps/mobile/src/app/ficha/[victimId].tsx` |
| M-FICHA-09 | Triagem: vermelho Atendimento imediato; amarelo Atendimento prioritário; verde Lesão leve; azul PCR com RCP; preto Morte evidente; branco Óbito constatado por médico; cinza Sem lesão aparente. Apoio: Start, C.Bombeiro, Ambulância, P.M.Rv/P.R.F., Nº da viatura de apoio, Transportado por. Ensinar seleção e leitura, nunca critério clínico. | `apps/mobile/src/fichas-de-vitimas/victim-triage-options.ts`, `packages/regras-de-resgate/src/far.ts` |
| M-FICHA-10 | Equipe médica: Médicos reguladores primário/secundário entre participantes; `Limpar seleção`; valor histórico sem correspondência; `Adicionar médico na ocorrência` quando ausente, `Ver equipe da ocorrência` em leitura. Nome/CRM são snapshots da regulação. | `apps/mobile/src/fichas-de-vitimas/medical-team-section.tsx` |

### Avaliação primária, sinais e escalas

| ID | Controles, resultados e condições | Fonte principal |
|---|---|---|
| M-FICHA-11 | X: Hemorragia exsanguinante; Hemorragia externa grave Sem resposta/SIM/NÃO; SIM revela Compressão direta/Torniquete. | `apps/mobile/src/fichas-de-vitimas/xabcde-section.tsx`, `packages/regras-de-resgate/src/operational-xabcde.ts` |
| M-FICHA-12 | A: Via aérea; Vias aéreas pérveas/obstruídas são exclusivas; Colar cervical, Aspiração, O2 máscara alta concentração, Abertura de vias aéreas, Cânula oro/nasofaríngea. | `apps/mobile/src/fichas-de-vitimas/xabcde-section.tsx`, `packages/regras-de-resgate/src/operational-xabcde.ts` |
| M-FICHA-13 | B: Ventilação; Cianose, Pneumotórax, Insuficiência respiratória, Hemotórax, Tórax instável; Intubação revela Motivo da intubação. | `apps/mobile/src/fichas-de-vitimas/xabcde-section.tsx`, `packages/regras-de-resgate/src/operational-xabcde.ts` |
| M-FICHA-14 | C: Circulação; perfusão Normal <2s/Diminuída >2s/Ausente; Choque, Hemorragia, Pulso ausente; Compressão torácica, Acesso venoso, Controle de hemorragias. | `apps/mobile/src/fichas-de-vitimas/xabcde-section.tsx`, `packages/regras-de-resgate/src/operational-xabcde.ts` |
| M-FICHA-15 | D: Neurológico; Miose/Midríase; Isocóricas/Anisocoria; Fotorreagentes/Consciente SIM/NÃO; anisocoria revela lado Esquerdo/Direito, isocoria limpa lado. | `apps/mobile/src/fichas-de-vitimas/xabcde-section.tsx`, `packages/regras-de-resgate/src/operational-xabcde.ts` |
| M-FICHA-16 | E: Exposição/Hipotermia; Aquecimento, Prancha, Retirada rápida, KED. Complementos FAR Permeabilização, Toracocentese, Cricostomia, Insuf. Respiratória, ECG< ou = 8, Desfibrilação aparecem abaixo e não dependem da letra aberta. | `apps/mobile/src/fichas-de-vitimas/xabcde-section.tsx`, `packages/regras-de-resgate/src/far-derived.ts` |
| M-FICHA-17 | Cinemática: multisseleção dos mecanismos; posição Motorista/Passag. frente/Passageiro atrás/Ejetado/Ciclista/Motociclista/Garupa/Outro. Proteção Sim/Não revela cinto, air bag, capacete, nenhum, ignorado, cadeirinha. Suspeitas etilismo, sono/cansaço, doença, negligência, problema veículo, condições atmosféricas, veículo parado/lento, ignorado. Vítimas fatais outras Não/Sim; Sim exige número. Impactos vêm da ocorrência. | `apps/mobile/src/fichas-de-vitimas/far-section-fields.tsx`, `packages/regras-de-resgate/src/operational-far.ts` |
| M-FICHA-18 | Sinais vitais: FR (ipm), FC (bpm), PA sistólica/diastólica conjunta, SpO2 (%); mensagens de valores fora da faixa. Complementos Final: FR/PA/FC/Sat O2; iniciais FAR são derivados. | `apps/mobile/src/app/ficha/[victimId].tsx`, `apps/mobile/src/fichas-de-vitimas/vital-warning-view.ts` |
| M-FICHA-19 | Glasgow: Abertura Ocular (Espontânea/À voz/À dor/Nenhuma), Resposta Verbal (Orientado/Confuso/Palavras inapropriadas/Sons-gemidos/Nenhuma), Resposta Motora (Obedece ordens/Localiza dor/Retira/Flexão anormal/Extensão/Nenhuma); total e classificação, RTS /12; AVDN A Alerta/V Verbal/D Dor/N Não resp. Sem entradas suficientes, total fica traço. | `apps/mobile/src/fichas-de-vitimas/glasgow-card-view.ts`, `apps/mobile/src/app/ficha/[victimId].tsx` |
| M-FICHA-20 | Idade <13 ou ausente adiciona Glasgow pediátrico (ocular/verbal/motora) e Trauma pediátrico (Peso, Vias aéreas, PA sistólica, Perfusão, Consciência, Lesão cutânea, Fratura). Grupos exclusivos; toque repetido desmarca. Totais parciais não inventam valor. Adulto permanece acima. | `apps/mobile/src/fichas-de-vitimas/pediatric-scales.tsx`, `packages/regras-de-resgate/src/pediatric-scales.ts` |

### Mapa corporal e evolução

| ID | Controles, resultados e condições | Fonte principal |
|---|---|---|
| M-FICHA-21 | Mapa corporal em tela cheia: Lesão/Queimadura, Frente/Costas, lateralidade, contagens, pinça/deslocamento no renderizador compatível; `CONCLUIR MAPA E VOLTAR À FICHA`. Cartão externo `ABRIR MAPA EM TELA CHEIA`. Marcas salvam a cada alteração. | `apps/mobile/src/fichas-de-vitimas/body-map-block.tsx`, `apps/mobile/src/fichas-de-vitimas/injury-body-map.tsx` |
| M-FICHA-22 | Lesão: tocar corpo cria marca numerada mesmo sem tipo; tocar marca seleciona, `Tipo da marca ativa` opcional, `Remover` explícito. Tipos Amputação/avulsão, Contusão, Corte, Laceração, Escoriação, Arma de fogo, Arma branca, Fratura exposta, Fratura fechada (suspeita), Perfurante, Queimadura, Outros + descrição. Atalhos coxa/perna direita/esquerda. Fora da silhueta exibe orientação. | `apps/mobile/src/fichas-de-vitimas/body-map-block.tsx`, `packages/regras-de-resgate/src/injury-marks.ts` |
| M-FICHA-23 | Queimadura: escolher Rápido — Regra dos Nove de Wallace ou Detalhado — Lund–Browder; Wallace indisponível para idade conhecida <16. Sem idade código permite Wallace e informa ausência. Escolher método não finaliza área; toque em região abre editor. | `apps/mobile/src/fichas-de-vitimas/body-map-block.tsx` |
| M-FICHA-24 | Editor: região, SCQ, slider Profundidade da queimadura — Epidérmica (não entra na SCQ), Dérmica superficial/média/profunda, Espessura total. Extensão Região inteira ou Área parcial · palma, −0,5/+0,5; `Concluir área`, `Remover`; limites explicam excesso da região. Frente e costas distintas. | `apps/mobile/src/fichas-de-vitimas/modelo-corporal-sfr/burn-region-editor.tsx`, `apps/mobile/src/fichas-de-vitimas/burn-depth-slider.tsx` |
| M-FICHA-25 | `Ver áreas salvas (N)` → Regiões avaliadas → Editar esta área. Troca de método com dados avisa que apaga avaliação: Cancelar/Confirmar troca. Histórico somente leitura pode `Iniciar nova avaliação`, Cancelar/Continuar, escolher método ou Cancelar nova avaliação. Idade incompatível gera bloqueio explicado. | `apps/mobile/src/fichas-de-vitimas/body-map-block.tsx` |
| M-FICHA-26 | Observações: Observações / SAMPLA e Evolução de Enfermagem, escrita e áudio por campo. Não existe ação de verso adicional. | `apps/mobile/src/app/ficha/[victimId].tsx` |
| M-FICHA-27 | Diagnóstico de enfermagem, 19 escolhas por código: 31 vias aéreas; 39 aspiração; 32 padrão ventilatório; 27 líquido; 24 perfusão; 122 percepção sensorial; 128 confusão; 5 temperatura; 86 neurovascular periférica; 132 dor; 46 pele; 44 tecido; 85 mobilidade; 38 trauma; 146 ansiedade; 148 medo; 140 violência autoinfligida; 51 comunicação; 4 infecção. Os rótulos integrais e grafias da UI estão no catálogo FAR; não ensinar indicação clínica. | `packages/regras-de-resgate/src/far.ts` |
| M-FICHA-28 | Medicação: Adicionar droga ou material, até 10 linhas. Natureza, Descrição, Qtde., Unidade mg/g/ml/gotas/ampola/comprimido/frasco/unidade; Remover. Origem armazenada não é campo visível. | `apps/mobile/src/fichas-de-vitimas/far-repeatable-list.tsx`, `packages/regras-de-resgate/src/far-repeatable-lists.ts` |
| M-FICHA-29 | Buscar no protocolo ou digitar; sugestões, Usar este texto (fora da lista), selo Fora da lista. Bolsas dinâmicas, Mais bolsas, Buscar nesta bolsa, Na ficha, Adicionar N itens à ficha; depois foco na primeira quantidade. Duplicadas e limite de dez linhas são explicados. | `apps/mobile/src/fichas-de-vitimas/material-kit-picker.tsx`, `apps/mobile/src/components/catalog-search-field.tsx` |
| M-FICHA-30 | Receita de psicotrópicos: Adicionar medicamento, até 10; Droga/Validade/Qtde./Lote/Remover. Médico assinante entre participantes; adicionar médico se ausente; Assinar/Assinar novamente, Assinando como…; dados pessoais derivados da identificação. | `apps/mobile/src/fichas-de-vitimas/far-section-fields.tsx`, `packages/regras-de-resgate/src/far-repeatable-lists.ts` |
| M-FICHA-31 | Desfecho, Bens e valores recolhidos: Adicionar item até 7; Item/Discriminação/Qtde./Obs./Remover. Recolhido por e Recebido por, cada um com RG/CPF e assinatura específicos da vítima. | `apps/mobile/src/fichas-de-vitimas/far-repeatable-list.tsx`, `packages/regras-de-resgate/src/far-repeatable-lists.ts` |
| M-FICHA-32 | Datas dd/mm/aaaa, data incompleta não salva; hora quatro dígitos/HH:MM e AGORA, limpar remove valor. Hora inválida mostra USE HH:MM; números são validados conforme campo. Rascunho parcial não equivale a gravação concluída. | `apps/mobile/src/fichas-de-vitimas/far-section-fields.tsx`, `apps/mobile/src/components/form.tsx` |
| M-FICHA-33 | Coincidência de identificação com outra ativa: Possível duplicidade, Continuar atendimento ou Sinalizar possível duplicidade; sinalização entra na fila, não bloqueia atendimento e não mescla pessoas automaticamente. | `apps/mobile/src/fichas-de-vitimas/victim-duplicate-warning.ts`, `apps/mobile/src/app/ficha/[victimId].tsx` |
| M-FICHA-34 | Variante de lesão pediátrica/legada sem modelo canônico: silhueta e regiões selecionáveis, Frente/Costas e `Regiões pequenas — botões grandes`; toque em região usa toggle, incluindo desmarcar no segundo toque. Não oferece o mesmo dock de tipo/marca numerada do adulto atual. Hipótese de compatibilidade observada no código; alcançabilidade no APK atual ainda precisa ser reproduzida. Se alcançável, remoção por segundo toque conflita com GLOBAL_TRUTH. | `apps/mobile/src/fichas-de-vitimas/body-map.tsx`, `apps/mobile/src/fichas-de-vitimas/body-map-block.tsx` |

### Desfecho, assinaturas e ciclo do registro

| ID | Controles, resultados e condições | Fonte principal |
|---|---|---|
| M-FECHO-01 | Resultado do atendimento: Recusa de atendimento, QTA, Aguardando no hospital, Liberado no local pelo médico; sair de recusa limpa seu tipo. Não há opção Óbito entre esses quatro resultados. | `apps/mobile/src/fichas-de-vitimas/outcome-section.tsx` |
| M-FECHO-02 | Recusa: atendimento/transporte; TERMO DE RESPONSABILIDADE; relatório da equipe médica e do socorrista/enfermeiro sobre orientação prestada; Modelo do termo, Atualizar texto com modelo, Texto do termo/data. Atalho textual “termo de recusa, sem lesões” ao sair do campo aplica modelo com identificação. Recusa indisponível em preto/branco/azul salvo histórico já existente; exigência definitiva dos relatórios ocorre na base. | `apps/mobile/src/fichas-de-vitimas/outcome-section.tsx`, `packages/regras-de-resgate/src/operational-signatures.ts` |
| M-FECHO-03 | Preto/branco/azul ou gravidade ausente revelam Óbito antes atend., Óbito na 1ª hora, Dia/Hora do Óbito. Cor sozinha não registra óbito. Alta dia ocultada para recusa/QTA; ocultação não apaga valores anteriores. Óbito registrado pode tornar assinatura da vítima Não aplicável — óbito. | `packages/regras-de-resgate/src/ficha-conditional-fields.ts`, `packages/regras-de-resgate/src/death-record.ts` |
| M-FECHO-04 | Assinatura da vítima: Coletar assinatura/Assinar novamente; Assine no campo abaixo, Limpar, Confirmar assinatura. Exige traço válido; falha preserva traço. Na recusa, mesma assinatura vale também para termo. | `apps/mobile/src/fichas-de-vitimas/signatures-section.tsx`, `apps/mobile/src/fechamento-do-atendimento/signature-pad.tsx` |
| M-FECHO-05 | Participantes da ocorrência abre equipe; assinaturas específicas da vítima mostram bens/receita/legadas/substituídas, identificação/registro/data/validade. Estado da ficha: Pode finalizar ou Revise os dados informados; Toque para ir ao campo leva ao passo pendente. | `apps/mobile/src/fichas-de-vitimas/signatures-section.tsx` |
| M-FECHO-06 | FINALIZAR FICHA → Finalizar ficha da Vítima N? → Cancelar/Finalizar; valida dados fornecidos, pode ser parcial. Hospital/liberado sem óbito exige assinatura da vítima. Recusa sem assinatura nunca coletada pode continuar parcial; assinatura invalidada exige nova. Ficha sem dados oferece Revisar ficha/Anular ficha, anular ainda exige justificativa. | `apps/mobile/src/fechamento-do-atendimento/ficha-finalize-confirmation.ts`, `packages/regras-de-resgate/src/ficha.ts` |
| M-FECHO-07 | Finalizada/histórico, anulada e reconciliada mostram somente leitura; VOLTAR PARA A OCORRÊNCIA. Finalizada continua bloqueada mesmo se ocorrência ativa. Complementação permite somente fichas ainda editáveis. | `apps/mobile/src/fechamento-do-atendimento/ficha-readonly.ts` |
| M-FECHO-08 | Alterar dado anterior confirmado/sincronizado ou de seção concluída em ficha editável abre Motivo da correção, Campos alterados, Descreva o motivo *, Cancelar/Confirmar, 3–500 caracteres. Cancelar mantém anterior. Primeiro preenchimento e marcas de lesão/queimadura são isentos desse gate. | `apps/mobile/src/fechamento-do-atendimento/correction-reason-modal.tsx`, `apps/mobile/src/fechamento-do-atendimento/victim-correction-gate.ts` |
| M-FECHO-09 | Substituir assinatura pede motivo; cancelar mantém traço e não salva. Mudança material do termo pode invalidar assinatura: Assinatura invalidada — assine novamente/Assinatura substituída. Versão histórica preservada. | `apps/mobile/src/fechamento-do-atendimento/refusal-signature-lifecycle.ts`, `apps/mobile/src/fechamento-do-atendimento/signature-replacement-reason.ts` |
| M-FECHO-10 | Anular ficha somente ativa/editável em ocorrência active; motivo 3–500, Cancelar/Anular ficha. Preserva número e enfileira alteração; não exclui histórico. | `apps/mobile/src/fechamento-do-atendimento/victim-lifecycle-sheet.tsx` |
| M-FECHO-11 | Segmento Anuladas → Restaurar ficha quando ocorrência ativa/editável; motivo, Cancelar/Restaurar ficha; preserva número. Não reabre finalizada ou reconciliada. | `apps/mobile/src/ocorrencias/victim-roster.tsx`, `apps/mobile/src/fechamento-do-atendimento/victim-lifecycle-view.ts` |
| M-FECHO-12 | Finalizar no local: exige vítima, todas fichas ativas concluídas e equipe/vínculos necessários. Botão mostra impedimento quando bloqueado. Confirmação Revisar/Finalizar informa protocolo e envio ao cliente; offline fica na fila. Áudio pendente não é bloqueio. | `apps/mobile/src/fechamento-do-atendimento/occurrence-closure-action.ts`, `apps/mobile/src/fechamento-do-atendimento/occurrence-finalize-confirmation.ts` |
| M-FECHO-13 | Sair e complementar depois exige vítima ativa, permite fichas incompletas; confirmação explica que ocorrência não está concluída. Fichas vazias são listadas com Revisar fichas/Continuar e complementar depois. Não criar outra ocorrência para continuar. | `apps/mobile/src/fechamento-do-atendimento/occurrence-finalize-confirmation.ts` |
| M-FECHO-14 | Reabrir pendente: Complementação retomada; continuar mesmas fichas editáveis e Finalizar ocorrência quando apta. | `apps/mobile/src/fechamento-do-atendimento/occurrence-closure-action.ts` |
| M-FECHO-15 | Adendo pós-fechamento: sem ação móvel encontrada. Necessidade deve ser tratada no fluxo disponível no painel, conforme seu próprio inventário; não representar correção como adendo. | `apps/mobile/src/fechamento-do-atendimento/ficha-readonly.ts` |

O catálogo FAR tem 19 blocos documentais: cabeçalho; horários/rodovia/destino; cena; identificação; veículo; gravidade/apoio; XABCDE; mecanismo; sinais; Glasgow; trauma/AVDN; avaliação secundária; SAMPLA/evolução; diagnóstico; drogas/materiais; receita; êxito/termo; bens; equipe legível. Os três primeiros são derivados da ocorrência. Equipe e suas assinaturas são coletadas uma vez na ocorrência. As antigas abas FAR permanecem como compatibilidade interna, não uma navegação adicional que o aluno precise procurar.

## Divergências, limites e itens que não podem ser inventados

1. `docs/fluxo-operacional.md` determina triagem antes da identificação. O componente atual `victim-creator-sheet.tsx` abre **Identifique a vítima**, depois **CONTINUAR PARA GRAVIDADE**. Registrar o comportamento real e encaminhar a divergência ao dono, sem reescrever a verdade global.
2. O botão **INICIAR CRONÔMETRO AGORA · OPCIONAL** do bloco Identificação fica desabilitado na rota atual porque `saveFarValuePatch` não é passado ao bloco. **MARCAR INÍCIO**, no cadastro inicial, existe. Mostrar a limitação, não um toque com resultado fabricado.
3. Documento oferece câmera para frente/verso, refotografia e remoção; não há opção explícita de galeria nessa superfície. Um status calculado em `document-photo-capture.ts` não é renderizado no bloco: não narrar esse texto como se aparecesse.
4. Não foi encontrada interface móvel para criar **adendo** em ocorrência encerrada nem para comandar reconciliação de duplicadas. A aba **Reconciliadas** consulta esse estado. Correção justificada de dado existente, restauração de ficha anulada e complementação de ocorrência pendente são operações distintas.
5. Ficha anulada pode ser restaurada com justificativa enquanto a ocorrência está ativa. Ocorrência finalizada não é reaberta por esse botão. **Sair e complementar depois** mantém o mesmo registro para complementação; não é finalização definitiva.
6. Em Escalas, Glasgow adulto/RTS continua visível junto às escalas pediátricas condicionais. Não dizer que uma interface substitui a outra.
7. `Mecanismos de trauma` ainda aparece em Cinemática; impactos são herdados da ocorrência. Não transformar comentário de código em descrição de uma tela que não existe.
8. Export web usa SQLite em memória (`apps/mobile/src/lib/db.ts`) enquanto o marcador de atualização remota persiste em armazenamento web. Recarregar pode esvaziar a lista e uma busca incremental retornar zero. Essa observação não comprova falha de persistência no Android; não usar export web para demonstrar continuidade após reinício.
9. Fotos de ocorrência têm limite 12, mas a tela atual inspecionada não expõe remoção de foto de cena/veículo equivalente à remoção da foto documental. Não ensinar um botão inexistente para contornar o limite.
10. O editor atual de queimadura usa profundidade por slider e extensão em palma com passos ±0,5; `docs/fluxo-operacional.md` descreve passos de 10%. São descrições diferentes. O roteiro registra a UI atual e a divergência, sem alterar a regra do produto.
11. O ramo de código de lesão pediátrica/legada usa segundo toque para desmarcar região; sua alcançabilidade no APK atual ainda não foi demonstrada. Se reproduzido, contraria a exigência de exclusão explícita do GLOBAL_TRUTH. O modelo adulto atual usa Remover explícito. Não generalizar um comportamento para o outro.
12. A folha de anular/restaurar bloqueia motivo menor que três caracteres sem explicar esse mínimo no texto visível. O tutorial pode informar a condição a partir do código, mas não afirmar que aparece uma mensagem que não existe.
13. Build de desenvolvimento pode mostrar `QA · falhar próximo salvamento` no pad. É controle interno e deve ficar fora das capturas operacionais; falhas encenadas por esse botão não contam como fluxo normal do usuário.

### Catálogo complementar das escalas pediátricas

Para M-FICHA-20, estes são os rótulos completos dos grupos condicionais em `packages/regras-de-resgate/src/pediatric-scales.ts`. Não são instruções de classificação clínica.

| Grupo | Opções |
|---|---|
| Glasgow — Abertura ocular | Espontânea; À solicitação verbal; À dor; Nenhuma. |
| Glasgow — Resposta verbal | Fala; Choro irritado; Chora à dor; Geme à dor; Nenhuma. |
| Glasgow — Resposta motora | Mov. espontânea; Localiza a dor; Retira aos estímulos; Flexão anormal; Extensão anormal; Nenhuma. |
| Trauma — Peso | > 20 kg; 11-20 kg; < 10 kg. |
| Trauma — Vias aéreas | Normal; Máscara / Guedel; Intubado / cricot. |
| Trauma — PA sistólica | > 90; 50-90; < 50. |
| Trauma — Perfusão | Boa perfusão; Sem pulso periférico; Sem pulso. |
| Trauma — Consciência | Desperto; Torporoso; Comatoso. |
| Trauma — Lesão cutânea | Sem lesão; Fechada / ruptura < 7cm; Laceração > 7cm / múltiplas. |
| Trauma — Fratura | Ausente; Única e fechada; Aberta / múltiplas. |

## Evidência exigida em execução

| Recurso | Evidência necessária antes de declarar demonstrado |
|---|---|
| Câmera, documento e foto de cena/veículo | Abertura real, permissão, captura, confirmação, miniatura persistida e destino correto. Câmera emulada deve ser identificada como tal. |
| Áudio e transcrição | Gravação nativa real, reprodução, envio, resultado do serviço e revisão humana. Não inserir texto de transcrição diretamente no banco ou na edição de vídeo. |
| OCR | Foto de documento explicitamente fictício, processamento real configurado, proposta de campos e confirmação/correção na UI. Se serviço indisponível, demonstrar captura e declarar OCR pendente. |
| GPS | Permissão e posição recebida pelo app. Posição emulada não é precisão física medida. |
| Offline e reinício | Registrar dados no Android, retirar rede, fechar/reabrir, encontrar dados, recuperar rede e confirmar recebimento na API local. |
| SQLCipher, backup, compartilhamento | Usar build que suporte os recursos, exportação real e validação; não inferir criptografia de um rótulo. O painel de evidência não substitui teste físico. |
| Atualização/bloqueio de versão | Cenário real controlado de versão. Build debug com Updates desativado não prova download/reinício OTA. |
| Conflitos | Duas versões reais da mesma ocorrência em ambiente local, comparação, decisão e convergência verificada. Demonstrar ambos os ramos em registros independentes. |
| Envio/PDF | PDF gerado pela API local após confirmação; requisição real e estados observados. E-mail `example.invalid` não comprova entrega a uma caixa postal. |
| Logout | Executar por último; evidenciar sessão encerrada e explicar limpeza local. Não gravar senha, token ou conteúdo do arquivo de backup em vídeo/relatório. |

Todo capítulo começa como **planejado**, mesmo que o recurso exista no código. Nenhum ID vira “demonstrado” apenas por estar citado na narração. O manifesto da gravação deve apontar arquivo, intervalo, ação executada, resultado e pendência de cada ID.

# Inventário do painel e da área operacional web — SFR

Auditoria de código local em **2026-09-07**. Escopo: interfaces acessíveis ao usuário, formulários, modais, filtros, ações e limitações do painel e ambiente operacional web. Não atesta gravação nem execução de cada recurso. Os IDs abaixo são o contrato de cobertura do `roteiro-web.json`; a coordenação deve vincular cada ID a uma cena real ou declarar a limitação.

A regra de produto é `GLOBAL_TRUTH.md`; este documento não cria exceções. O inventário foi produzido sem executar o app. Depois, a coordenação autorizou a correção de autorização descrita ao final e seus testes locais. Não houve pnpm, commit ou publicação por esta tarefa.

## Sequência de treinamento

| Capítulo | Assunto | Alvo | Recursos |
|---|---|---:|---:|
| W01 | Entrar e reconhecer seu ambiente | 110 s | 6 |
| W02 | Encontrar e acompanhar ocorrências | 110 s | 9 |
| W03 | Ler a ficha, consultar anexos e emitir documentos | 110 s | 11 |
| W04 | Gerar relatórios e consultar indicadores | 110 s | 6 |
| W05 | Criar pessoas e administrar acessos | 110 s | 9 |
| W06 | Manter viaturas e participantes operacionais | 110 s | 9 |
| W07 | Cadastrar bases, concessionárias e clientes destinatários | 110 s | 8 |
| W08 | Preparar materiais, farmácia e versões do FAR | 110 s | 7 |
| W09 | Criar uma ocorrência operacional e organizar a equipe | 110 s | 10 |
| W10 | Preencher vítimas e o FAR com revisão consciente | 130 s | 16 |
| W11 | Resolver pendências e preservar o histórico clínico | 130 s | 14 |
| W12 | Solicitar cópias, acompanhar entregas e consultar auditoria | 110 s | 11 |

Total planejado web: **22 min 40 s**, 116 recursos. Somar o módulo mobile para fechar o treinamento de 20–40 minutos; encurtar pausas ou dividir edição avançada sem remover cobertura. A duração final depende de áudio e execução, não desta estimativa.

## Papéis, sessões e diferenças observadas

| Identidade | Permissões e recortes reais |
|---|---|
| `admin` — NexUnio (plataforma) | Matriz completa, inclusive `platform:operate`; concessão e simulação também dependem de allowlist. Revisão clínica, emenda, duplicidades e processamento de mídia possuem condições próprias. |
| `org_admin` — Admin SFR | Matriz completa exceto plataforma. A UI atual de coordenação de fechamento/emenda/duplicidades/cópias tem condições literais que não incluem este papel; não afirmar equivalência integral com `admin`. |
| `coordenador` — Coordenação | `panel:view`, `operations:manage`, `fleet:manage`, revisão/leitura interna e pública de ocorrências, gestão de entregas. Não ganha `users:manage`, `settings:operate` ou `far:manage`. |
| `concessionaria` | Consulta pública e painel conforme escopo da concessionária; não edição clínica ou cadastros administrativos. |
| `cliente` | Matriz de leitura pública, com corte adicional: somente `/ocorrencias` e `/api/occurrences`, limitado a `clientId` vinculado. Sem vínculos, não vê ocorrências. |
| Ambulância | Sessão operacional separada, código/senha da viatura e escopo da própria viatura/base/concessionária. Cria ocorrência e vítima; não concede papel administrativo. |
| Funções clínicas | Socorrista, enfermeiro, técnico de enfermagem e médico são funções de participação/assinatura; não papéis do painel. Registro profissional e signatário precisam corresponder. |

Fontes: `packages/regras-de-resgate/src/rbac.ts`, `apps/web/src/lib/clientPortalRoutes.ts`, `apps/web/src/lib/clientPortal.ts`, `apps/web/src/proxy.ts`, `apps/web/src/lib/occurrenceAccess.ts`, `apps/web/src/app/operacional/ocorrencias/[id]/page.tsx`, `packages/regras-de-resgate/src/multi-victim.ts`.

Condições especiais verificadas: `canProcessClinicalMedia` somente `admin`; `canCoordinateClosure`, `canAmendOccurrenceAfterClose` e `canResolveVictimDuplicate` somente `admin`/`coordenador`; `delivery-panel.canRequest` inclui `admin`, `coordenador` e legado `coordinator`, mas não `org_admin`. Esta diferença deve ser filmada com o perfil correto, sem reinterpretação da política.

## Rotas de interface

- `/configuracoes/auditoria` — `apps/web/src/app/configuracoes/auditoria/page.tsx`
- `/configuracoes/catalogo` — `apps/web/src/app/configuracoes/catalogo/page.tsx`
- `/configuracoes/clientes` — `apps/web/src/app/configuracoes/clientes/page.tsx`
- `/configuracoes/far` — `apps/web/src/app/configuracoes/far/page.tsx`
- `/configuracoes/farmacia` — `apps/web/src/app/configuracoes/farmacia/page.tsx`
- `/configuracoes/materiais` — `apps/web/src/app/configuracoes/materiais/page.tsx`
- `/configuracoes` — `apps/web/src/app/configuracoes/page.tsx`
- `/configuracoes/participantes/[id]` — `apps/web/src/app/configuracoes/participantes/[id]/page.tsx`
- `/configuracoes/participantes/novo` — `apps/web/src/app/configuracoes/participantes/novo/page.tsx`
- `/configuracoes/participantes` — `apps/web/src/app/configuracoes/participantes/page.tsx`
- `/configuracoes/simular` — `apps/web/src/app/configuracoes/simular/page.tsx`
- `/dashboard` — `apps/web/src/app/dashboard/page.tsx`
- `/indicadores` — `apps/web/src/app/indicadores/page.tsx`
- `/login` — `apps/web/src/app/login/page.tsx`
- `/ocorrencias/[id]` — `apps/web/src/app/ocorrencias/[id]/page.tsx`
- `/ocorrencias` — `apps/web/src/app/ocorrencias/page.tsx`
- `/operacional/login` — `apps/web/src/app/operacional/login/page.tsx`
- `/operacional/ocorrencias/[id]` — `apps/web/src/app/operacional/ocorrencias/[id]/page.tsx`
- `/operacional/ocorrencias/nova` — `apps/web/src/app/operacional/ocorrencias/nova/page.tsx`
- `/operacional` — `apps/web/src/app/operacional/page.tsx`
- `/` — `apps/web/src/app/page.tsx`
- `/relatorios` — `apps/web/src/app/relatorios/page.tsx`
- `/usuarios/[id]` — `apps/web/src/app/usuarios/[id]/page.tsx`
- `/usuarios/novo` — `apps/web/src/app/usuarios/novo/page.tsx`
- `/usuarios` — `apps/web/src/app/usuarios/page.tsx`
- `/viaturas/[id]` — `apps/web/src/app/viaturas/[id]/page.tsx`
- `/viaturas/nova` — `apps/web/src/app/viaturas/nova/page.tsx`
- `/viaturas` — `apps/web/src/app/viaturas/page.tsx`

## W01 — Entrar e reconhecer seu ambiente

| ID | Tela/ação | Como operar e concluir |
|---|---|---|
| W01-01 | Login do painel | Abrir /login, preencher Email e Senha, clicar Entrar; mostrar erro de credenciais inválidas sem filmar a senha. |
| W01-02 | Identidade e navegação | Conferir identidade/papel no rodapé e itens visíveis da barra lateral; navegar por um item e usar o link de pular para o conteúdo quando necessário. |
| W01-03 | Papéis e recortes | Comparar menus de Admin SFR, Coordenação, Concessionária e Cliente; no Cliente abrir apenas /ocorrencias e explicar vínculos de clientes destinatários. |
| W01-04 | Login operacional | Abrir /operacional/login, preencher Código da ambulância e Senha operacional, clicar Entrar no ambiente operacional; conferir viatura/base. |
| W01-05 | Saída operacional | Na moldura operacional usar a marca para retornar a /operacional e o botão Sair para encerrar a sessão da viatura. |
| W01-06 | Função clínica e permissão | Na identificação da equipe distinguir função profissional e conselho/número dos papéis de acesso do painel; apontar ausência de controle quando o perfil não o recebe. |

Fontes: `apps/web/src/app/login/page.tsx`, `apps/web/src/app/operacional/login/page.tsx`, `apps/web/src/app/operacional/OperationalFrame.tsx`, `apps/web/src/components/sidebar.tsx`, `apps/web/src/components/topbar.tsx`, `apps/web/src/lib/clientPortalRoutes.ts`, `apps/web/src/lib/clientPortal.ts`, `apps/web/src/proxy.ts`, `packages/regras-de-resgate/src/rbac.ts`.

Limitações e cuidado de gravação: Não gravar credenciais, tokens ou dados reais. Não prometer que org_admin possui os comandos que usam uma condição literal admin/coordenador. Não inventar botão Sair ou recuperação de senha na casca do painel.

## W02 — Encontrar e acompanhar ocorrências

| ID | Tela/ação | Como operar e concluir |
|---|---|---|
| W02-01 | Dashboard: período e recorte | Em /dashboard selecionar período e concessionária/base disponíveis; clicar Filtrar e depois Atualizar. |
| W02-02 | Dashboard: cartões | Localizar Ocorrências, Vítimas, Tempo de resposta, Saída da base e Pendências de sincronização; ler unidade, ausência de valor e comparação quando apresentada. |
| W02-03 | Dashboard: gráficos | Percorrer ocorrências por dia, gravidade, mecanismos e tempo por base sem interpretar o seed como desempenho real. |
| W02-04 | Dashboard: atalhos | Abrir uma ocorrência recente, usar Ver todas e localizar Exportar relatório. |
| W02-05 | Busca global | Digitar termo em Buscar ocorrência e submeter para /ocorrencias?q=termo. |
| W02-06 | Ocorrências: pesquisa e período | Em /ocorrencias combinar busca textual, período Todos/7/30/90 dias e datas De/Até; aplicar Filtrar; usar Limpar intervalo. |
| W02-07 | Ocorrências: recortes | Combinar status, tipo Trauma/USA/USB, concessionária, base e psicotrópicos se visível; explicar filtros condicionais por permissão. |
| W02-08 | Ocorrências: resultados e navegação | Abrir registro, voltar, percorrer Anterior/próxima/página; no vazio usar Limpar filtros. |
| W02-09 | Situação e criação | Mostrar estados da ocorrência e pendências de sync; abrir Nova ocorrência e reconhecer eventual login operacional. |

Fontes: `apps/web/src/app/dashboard/page.tsx`, `apps/web/src/app/ocorrencias/page.tsx`, `apps/web/src/components/topbar.tsx`, `apps/web/src/indicadores-operacionais/dashboard-stats.ts`.

Limitações e cuidado de gravação: Dados sintéticos não são KPIs de produção. Busca por nome não autoriza exposição de dados pessoais no tutorial. Nova ocorrência não cria pela identidade administrativa do painel.

## W03 — Ler a ficha, consultar anexos e emitir documentos

| ID | Tela/ação | Como operar e concluir |
|---|---|---|
| W03-01 | Detalhe da ocorrência | Abrir /ocorrencias/[id]; conferir cabeçalho, dados da ocorrência, equipe, horários/durações, QTH, cinemática, avaliação da cena e destino/encerramento. |
| W03-02 | Leitura das vítimas | Selecionar vítima e expandir identificação, XABCDE, vitais iniciais/finais, Glasgow/AVDN, dados pediátricos quando presentes, observações, desfecho, assinaturas e Campos FAR preenchidos. |
| W03-03 | Estados recebidos | Reconhecer aviso Sincronizada com pendências e distinguir recebimento de revisão concluída. |
| W03-04 | Anexos | Abrir frente/verso de documento e fotos da ocorrência; no editor ampliar miniatura em tamanho real e fechar pelo controle Fechar anexo ampliado. |
| W03-05 | OCR: processamento | Com perfil habilitado e documento pendente, clicar Processar OCR da frente/do verso; filmar mensagem real de indisponibilidade local sem fabricar extração. |
| W03-06 | OCR: revisão | Quando houver resultado real disponível, comparar Nome/RG/CPF/Data de nascimento, selecionar apenas sugestões válidas sem conflito e clicar Aplicar dados revisados; manter como demonstração condicional enquanto o provedor estiver desativado. |
| W03-07 | Áudio e transcrição | Localizar áudio de ocorrência/vítima/campo, reproduzir/pausar/buscar posição; identificar pendente de upload/transcrição/revisão, revisado, falha e mensagens de arquivo indisponível. |
| W03-08 | Voz: revisão | Com provedor disponível solicitar Processar transcrições; em resultado pendente conferir escopo e sugestões, editar Texto revisado e clicar Aplicar revisão; no local gravar só estados disponíveis. |
| W03-09 | Prontuário PDF | Clicar Prontuário PDF e conferir documento da ocorrência com vítimas/equipe; registrar emissão real local. |
| W03-10 | PDF individual e óbito | Selecionar Ficha desta vítima e verificar o PDF individual; localizar documento de óbito apenas quando a condição real o disponibilizar. |
| W03-11 | Abrir editor | Usar Editar operacional quando visível; retornar ao detalhe pelo botão Voltar. |

Fontes: `apps/web/src/app/ocorrencias/[id]/page.tsx`, `apps/web/src/app/ocorrencias/[id]/ProcessDocumentOcrButton.tsx`, `apps/web/src/app/ocorrencias/[id]/DocumentOcrReviewForm.tsx`, `apps/web/src/app/ocorrencias/[id]/ProcessVoiceDraftsButton.tsx`, `apps/web/src/app/ocorrencias/[id]/VoiceDraftReviewForm.tsx`, `apps/web/src/lib/voiceDraftReviewView.ts`, `apps/web/src/app/operacional/ocorrencias/[id]/OperationalMediaPanel.tsx`, `apps/web/src/app/operacional/ocorrencias/[id]/OperationalAudioPanel.tsx`, `apps/web/src/components/SeekableAudioPlayer.tsx`.

Limitações e cuidado de gravação: OCR/transcrição estão desativados na bancada local; não mostrar resultados simulados como execução real. Campos/sugestões e anexos devem ser sintéticos e não conter credenciais. Baixar PDF não significa enviar e-mail.

## W04 — Gerar relatórios e consultar indicadores

| ID | Tela/ação | Como operar e concluir |
|---|---|---|
| W04-01 | Relatórios: filtros | Abrir /relatorios, selecionar período 30/90 dias, concessionária e psicotrópicos quando disponível; clicar Aplicar. |
| W04-02 | Relatórios: resumo | Conferir totais, gravidade, mecanismos e tempo de resposta por base; reconhecer vazio e escopo público/interno. |
| W04-03 | Exportações | Usar Ver exportações, baixar Excel e PDF, abrir ambos e conferir período/filtros no resultado. |
| W04-04 | Indicadores: filtros | Em /indicadores selecionar período 7/30/90/365 dias, concessionária/base e clicar Aplicar filtros. |
| W04-05 | Indicadores: cartões e sync | Ler quatro cartões, comparação anterior, unidades e estado de sincronização; distinguir ausência de medida de zero. |
| W04-06 | Indicadores: gráficos | Localizar Tempo de resposta por base, Meta de resposta, Gravidade, Horários de pico, Mecanismos e Concessionárias; conferir recorte do gráfico. |

Fontes: `apps/web/src/app/relatorios/page.tsx`, `apps/web/src/lib/reportExportActionsView.ts`, `apps/web/src/app/indicadores/page.tsx`, `apps/web/src/lib/operationalIndicatorsView.ts`.

Limitações e cuidado de gravação: Não chamar metas exibidas de norma clínica ou contratual. Não apresentar números da base fictícia como benefício medido do produto. Não ampliar o escopo de acesso no arquivo exportado.

## W05 — Criar pessoas e administrar acessos

| ID | Tela/ação | Como operar e concluir |
|---|---|---|
| W05-01 | Lista de pessoas | Abrir /usuarios, conferir tabela Painel (computador), nome/e-mail/papel/base/status e botão Novo usuário; não inventar filtros que não estão na tela. |
| W05-02 | Criar usuário | Em /usuarios/novo preencher Nome, Email, Senha inicial, Papel, Base, Concessionária e Conselho/número quando pertinentes; clicar no botão de criação. |
| W05-03 | Gerador de senha inicial | Identificar Gerar no formulário: preenche senha inicial; manter valor fora do vídeo e concluir criação somente no cenário local autorizado. |
| W05-04 | Vínculos do Cliente | Selecionar papel Cliente e concessionária, marcar clientes destinatários; explicar vazio e necessidade de cadastro prévio. |
| W05-05 | Editar usuário | Abrir Editar, conferir identificação, ajustar papel/base/concessionária/registro/vínculos disponíveis, Salvar alterações e voltar à lista. |
| W05-06 | Ativar ou desativar usuário | Em edição usar Desativar/Ativar em conta sintética descartável; conferir status e explicar impacto imediato no acesso. |
| W05-07 | Redefinir senha | Na seção própria preencher nova senha, opcionalmente Gerar, e clicar Redefinir senha; nunca mostrar o valor. |
| W05-08 | Acesso da viatura | Na área Campo (viatura), conferir identidade, código de login, base e acesso ativo; abrir edição da viatura. |
| W05-09 | Senha operacional imediata | Identificar Gerar senha do quadro Campo como aplicação imediata e exibição única; não clicar durante gravação aberta de credenciais. |

Fontes: `apps/web/src/app/usuarios/page.tsx`, `apps/web/src/app/usuarios/novo/NovoUsuarioForm.tsx`, `apps/web/src/app/usuarios/[id]/EditarUsuarioForm.tsx`, `apps/web/src/app/usuarios/SenhaViaturaForm.tsx`, `packages/regras-de-resgate/src/rbac.ts`.

Limitações e cuidado de gravação: SenhaViaturaForm gera e aplica imediatamente; não usar como prévia. Ativar/desativar e redefinir senha alteram acesso; somente entidades locais sintéticas. Não gravar senhas, mesmo sintéticas.

## W06 — Manter viaturas e participantes operacionais

| ID | Tela/ação | Como operar e concluir |
|---|---|---|
| W06-01 | Frota: filtros e cartões | Abrir /viaturas, pesquisar prefixo/placa e filtrar status disponível/manutenção/inativa, tipo USA/USB e base; ler totais e abrir cartão. |
| W06-02 | Criar viatura | Abrir Nova viatura; preencher prefixo, nome visual, código login, senha operacional, placa opcional, tipo, base, status e Login operacional ativo; salvar sem expor senha. |
| W06-03 | Editar viatura | Abrir edição, alterar campos e Salvar alterações; senha vazia preserva a atual, nova senha substitui após salvar. |
| W06-04 | Dar baixa e reativar | Usar Dar baixa em viatura sintética, conferir inativa; editar Status e acesso operacional para reativação autorizada. |
| W06-05 | Importar viaturas | Abrir importação, ler cabeçalho prefixo/nome/placa/tipo/base/status/login/senha/authAtiva, selecionar ou colar CSV e Importar CSV; revisar linhas criadas/ignoradas/inválidas sem mostrar a coluna de senha. |
| W06-06 | Listar participantes | Abrir /configuracoes/participantes e conferir nome, função, registro, concessionária e status; localizar Novo participante e Editar. |
| W06-07 | Criar participante | Preencher nome, função, conselho, número profissional e concessionária; salvar; distinguir diretório da equipe de usuário do painel. |
| W06-08 | Editar e ativar participante | Abrir Editar, modificar dados disponíveis, Salvar alterações e demonstrar Ativar/Desativar apenas em registro sintético. |
| W06-09 | Importar participantes | Selecionar ou colar CSV nome/concessionaria/funcao/registro/tipoRegistro; Importar CSV; ler contagens e motivos por linha. |

Fontes: `apps/web/src/app/viaturas/page.tsx`, `apps/web/src/app/viaturas/nova/NovaViaturaForm.tsx`, `apps/web/src/app/viaturas/[id]/EditarViaturaForm.tsx`, `apps/web/src/app/configuracoes/participantes/page.tsx`, `apps/web/src/app/configuracoes/participantes/novo/NovoParticipanteForm.tsx`, `apps/web/src/app/configuracoes/participantes/[id]/EditarParticipanteForm.tsx`, `apps/web/src/importacao-de-cadastros/bulk-import-card.tsx`.

Limitações e cuidado de gravação: Importação CSV grava imediatamente; duplicados são ignorados, não atualizados. Status da frota e authAtiva são controles diferentes. Não alterar entidades remotas para gravar.

## W07 — Cadastrar bases, concessionárias e clientes destinatários

| ID | Tela/ação | Como operar e concluir |
|---|---|---|
| W07-01 | Bases: cadastro manual | Em /configuracoes/catalogo preencher nome, concessionária, nome visual, código operacional, rodovia, km inicial/final, sentido, observações e Base ativa; salvar. |
| W07-02 | Bases: consulta e edição | Conferir grupos e contagens de concessionárias/bases ativas/inativas; usar Editar de uma base, alterar e salvar; usar Novo quando presente para limpar edição. |
| W07-03 | Logo da concessionária | Selecionar concessionária e Arquivo do logo, conferir prévia e salvar; demonstrar cancelamento da confirmação de remover logo antes de uma remoção local autorizada. |
| W07-04 | Importar bases | Selecionar/colar CSV com campos da interface; Importar CSV e ler criadas/ignoradas/inválidas; explicar checkbox de retirada do catálogo-modelo, mantendo-o desligado salvo cenário específico. |
| W07-05 | Cliente destinatário | Em /configuracoes/clientes preencher nome, concessionária, e-mails, regra sob solicitação/automática no fechamento, logo e Cliente ativo; Salvar cliente. |
| W07-06 | Editar cliente e logo | Selecionar Editar, alterar campos, remover logo pelo controle explícito se necessário e salvar; Novo inicia outro cadastro. |
| W07-07 | Importar clientes | Carregar CSV concessionaria/nome/emails/regraEnvio/ativo; separar vários e-mails pelo formato documentado no cartão; importar e revisar resultado. |
| W07-08 | Vincular consulta | Retornar à edição de usuário Cliente, selecionar os clientes destinatários autorizados e salvar vínculos; conferir acesso restrito. |

Fontes: `apps/web/src/app/configuracoes/catalogo/CatalogoClient.tsx`, `apps/web/src/app/configuracoes/clientes/OccurrenceClientsClient.tsx`, `apps/web/src/importacao-de-cadastros/bulk-import-card.tsx`, `apps/web/src/app/usuarios/novo/NovoUsuarioForm.tsx`.

Limitações e cuidado de gravação: Configurar envio automático pode gerar solicitações ao fechar; usar somente base local com entregas externas desligadas. Retirada do catálogo-modelo é efeito adicional da importação; não selecionar por padrão. Logos devem ser autorizados e conter somente marca de demonstração.

## W08 — Preparar materiais, farmácia e versões do FAR

| ID | Tela/ação | Como operar e concluir |
|---|---|---|
| W08-01 | Materiais: criar | Em /configuracoes/materiais preencher Nome, Tipo droga/material, Unidade opcional e Salvar item; esclarecer que não é estoque. |
| W08-02 | Materiais: editar e desativar | Na tabela usar Editar, salvar alteração, usar Novo para limpar; Desativar somente item local sintético. |
| W08-03 | Materiais: importar com prévia | Selecionar CSV/XLSX com nome/tipo/unidade, conferir linhas válidas e inválidas, Confirmar importação ou Cancelar; distinguir da importação CSV dos demais cadastros. |
| W08-04 | Farmácia | Em /configuracoes/farmacia preencher o único e-mail autorizado e salvar; mostrar que vazio mais salvar remove configuração; manter entregas locais indisponíveis. |
| W08-05 | FAR: versão e histórico | Em /configuracoes/far conferir versão, vigência, páginas/seções/campos e histórico publicado. |
| W08-06 | Protocolo: publicar catálogo | Ler versão atual e itens, preencher somente catálogo aprovado no formato Categoria · Item, uma linha por item, clicar Publicar protocolo e conferir nova versão. |
| W08-07 | FAR: publicar manifesto | Conferir JSON de manifesto aprovado, clicar Publicar FAR somente em cenário local autorizado e ler sucesso/erros; não ensinar edição clínica do protocolo. |

Fontes: `apps/web/src/app/configuracoes/materiais/MaterialCatalogClient.tsx`, `apps/web/src/app/configuracoes/farmacia/PharmacySettingsClient.tsx`, `apps/web/src/app/configuracoes/far/page.tsx`, `apps/web/src/app/configuracoes/far/FarCatalogClient.tsx`, `apps/web/src/app/configuracoes/far/FarProtocolClient.tsx`, `packages/regras-de-resgate/src/far-runtime-catalog.ts`.

Limitações e cuidado de gravação: Publicar catálogo/FAR modifica opções e versões; não inventar conteúdo clínico. Farmácia é configuração de destinatário; não expor endereço real nem disparar entrega externa. A prévia e confirmação pertencem à importação de materiais, não às demais cargas CSV.

## W09 — Criar uma ocorrência operacional e organizar a equipe

| ID | Tela/ação | Como operar e concluir |
|---|---|---|
| W09-01 | Lista operacional | Em /operacional alternar Todas, Ativas, Fechamento e Concluídas; abrir ocorrência e usar Limpar filtro no vazio. |
| W09-02 | Criar ocorrência | Em Nova ocorrência conferir Data e hora, Número opcional, Rodovia, Quilômetro e modo base_review/on_scene; Criar e abrir ocorrência; confirmar viatura/base vindas da sessão. |
| W09-03 | Navegação do editor | Identificar contexto/situação/Voltar, áreas Ocorrência/Vítimas/FAR/Mídia/Áudio/Fechamento; usar abas e setas/Home/End quando necessário; conferir avisos laterais. |
| W09-04 | Dados e horários | Em Ocorrência preencher acionamento, saída da base, chegada ao local/hospital e retorno à base; conferir QTH latitude/longitude/precisão/momento, tipo de rodovia/nome/km e destino. |
| W09-05 | Cena e contexto | Preencher avaliação da cena, tempo preso em ferragens, veículos/pessoas envolvidos, mecanismo, impacto, posição e proteção conforme dados registrados; não escolher condutas pelo tutorial. |
| W09-06 | Cliente e cópias no contexto | Localizar protocolo do cliente, pedido de cópia, hospital/e-mail quando disponível, política de assinatura e responsável; salvar pelo comando Salvar ocorrência. |
| W09-07 | Adicionar participantes | Na área Fechamento clicar Adicionar participante, digitar pelo menos dois caracteres do nome, selecionar sugestão do diretório e conferir função/conselho/número. |
| W09-08 | Responsável e assinatura | Marcar o responsável, capturar assinatura sintética no quadro, usar Limpar quando necessário; explicar assinatura invalidada após mudança de função; não ensinar assinatura por outra pessoa. |
| W09-09 | Remover e salvar equipe | Usar Remover participante explicitamente, Salvar participantes, conferir resultado; em divergência de versão revisar equipe do servidor antes de nova tentativa. |
| W09-10 | Pendências de mídia e áudio | Abrir Mídia e Áudio, reconhecer arquivos já sincronizados, contagens e indisponibilidade; retornar ao aparelho de origem para anexar/sincronizar arquivos ausentes. |

Fontes: `apps/web/src/app/operacional/page.tsx`, `apps/web/src/app/operacional/ocorrencias/nova/page.tsx`, `apps/web/src/app/operacional/ocorrencias/nova/CreateOccurrenceButton.tsx`, `apps/web/src/app/operacional/ocorrencias/[id]/OperationalEditor.tsx`, `apps/web/src/app/operacional/ocorrencias/[id]/OperationalOccurrenceForm.tsx`, `apps/web/src/app/operacional/ocorrencias/[id]/OperationalTeamEditor.tsx`, `apps/web/src/app/operacional/ocorrencias/[id]/OperationalPendingRail.tsx`, `apps/web/src/lib/operationalEditorFields.ts`.

Limitações e cuidado de gravação: Não alegar que a equipe é informada na criação inicial. O editor web recebe mídias; a captura no aparelho é assunto do módulo mobile. Preenchimento de controles não é recomendação clínica.

## W10 — Preencher vítimas e o FAR com revisão consciente

| ID | Tela/ação | Como operar e concluir |
|---|---|---|
| W10-01 | Vítimas: criar e selecionar | Em sessão de ambulância clicar Adicionar vítima; selecionar número/nome corretos, alternar Ativas/Anuladas/Reconciliadas e navegar por páginas disponíveis. |
| W10-02 | Identificação | Em Dados preencher nome, sexo, idade, placa e tipo de veículo; conferir identificação antes de mudar de ficha. |
| W10-03 | Triagem e XABCDE | Abrir Triag e X/A/B/C/D/E; registrar avaliação/conduta já documentadas, observar exclusividade e campos condicionais, sem ensinar decisão clínica; ver apêndice de controles. |
| W10-04 | Vitais e Glasgow | Abrir Vitais e Glasgow; preencher valores documentados, conferir unidades e total derivado; ausência de avaliação não é um valor clínico padrão. |
| W10-05 | Observações e desfecho | Em Obs preencher observações/evolução; em Desfecho registrar destino, recusa quando cabível, dados de identificação/endereço, sinais finais, datas de óbito/alta quando pertinentes. |
| W10-06 | Assinaturas da ficha | Abrir Assinat e conferir assinaturas vinculadas; essa aba lista assinaturas, a captura da equipe/FAR usa os controles próprios. |
| W10-07 | Salvar ficha e versão | Clicar Salvar ficha após alterações; se vier atualização do servidor, revisar dados atuais e salvar de novo; não sobrescrever conflito sem conferência. |
| W10-08 | Revisão de IA | Identificar Preenchido pela IA · revisar, conferir campo e Confirmar; se corrigir o valor, salvar a ficha/FAR pelo botão correspondente. |
| W10-09 | FAR: navegação e derivados | Selecionar vítima e versão em FAR; percorrer 2 páginas e 19 seções do catálogo base; reconhecer campos derivados/promovidos e corrigir na origem; catálogo publicado pode mudar estrutura. |
| W10-10 | FAR: mecanismo e fatalidades | Conferir grupos exclusivos/múltiplos, detalhes condicionais de proteção/posição e outras vítimas fatais com quantidade quando habilitada. |
| W10-11 | FAR: listas | Adicionar/remover explicitamente linhas de Drogas e materiais (10), Receita de psicotrópicos (10), Bens e valores (7); preencher descrição/quantidade/unidade, droga/validade/quantidade/lote ou item/discriminação/quantidade/observação. |
| W10-12 | FAR: bolsas e catálogo | Abrir bolsa/maleta ou Abrir mais bolsas, selecionar categoria, buscar, marcar itens não presentes e Adicionar à ficha; respeitar vagas restantes da lista e conferir origem/unidade. |
| W10-13 | FAR: mapa corporal | Consultar frente/verso, regiões e marcas; projeção de marcas estruturadas é somente leitura no web; não ensinar remoção por segundo toque no mapa legado. |
| W10-14 | FAR: queimaduras | Conferir registro histórico e método; iniciar nova avaliação só após confirmação explícita; Wallace solicita região/profundidade, Lund-Browder exige localização feita no app; conferir profundidade/cobertura/unidades existentes e avisos, sem sugerir avaliação clínica. |
| W10-15 | FAR: assinaturas | Em assinatura FAR usar Ver/Substituir assinatura, conferir nome/registro e participante médico quando exigido; capturar somente assinatura do signatário, Salvar assinatura ou Cancelar substituição. |
| W10-16 | Salvar FAR | Clicar Salvar FAR, ler confirmação/validação e revisar avisos de campos obrigatórios sem preencher dados fictícios como atendimento real. |

Fontes: `apps/web/src/app/operacional/ocorrencias/[id]/CreateVictimButton.tsx`, `apps/web/src/app/operacional/ocorrencias/[id]/VictimPageSwitcher.tsx`, `apps/web/src/app/operacional/ocorrencias/[id]/OperationalVictimForm.tsx`, `apps/web/src/app/operacional/ocorrencias/[id]/XabcdeFields.tsx`, `apps/web/src/app/operacional/ocorrencias/[id]/OutcomeFields.tsx`, `apps/web/src/app/operacional/ocorrencias/[id]/AiPendingReviewField.tsx`, `apps/web/src/app/operacional/ocorrencias/[id]/OperationalFarEditor.tsx`, `apps/web/src/app/operacional/ocorrencias/[id]/MaterialKitPicker.tsx`, `apps/web/src/app/operacional/ocorrencias/[id]/OperationalRepeatableList.tsx`, `apps/web/src/app/operacional/ocorrencias/[id]/BodyMapField.tsx`, `apps/web/src/fichas-de-vitimas/burn-assessment-field.tsx`, `packages/regras-de-resgate/src/far.ts`, `packages/regras-de-resgate/src/far-repeatable-lists.ts`, `packages/regras-de-resgate/src/operational-xabcde.ts`.

Limitações e cuidado de gravação: As listas completas de campos ficam no inventário; não narrar condutas ou valores para situações clínicas. Mapa estruturado e nova localização Lund-Browder não são editáveis no web. Mapa legado/Wallace contém toggle de região; não normalizar remoção por segundo toque, incompatível com GLOBAL_TRUTH. Catálogo publicado pode acrescentar campos; conferir versão real da bancada.

## W11 — Resolver pendências e preservar o histórico clínico

| ID | Tela/ação | Como operar e concluir |
|---|---|---|
| W11-01 | Pendências de fechamento | Abrir Fechamento, ler motivo visível e rail de pendências de vítimas/áudios/assinaturas/sync; corrigir na área indicada antes de repetir. |
| W11-02 | Aparelhos e barreira | Em status closing consultar Sincronização dos aparelhos, Pendente/Sincronizado/Dispensado, último contato e duplicidades pendentes; não prometer atualização automática. |
| W11-03 | Dispensar aparelho | Com admin/coordenador abrir Dispensar aparelho, informar motivo 3–500 caracteres e Confirmar dispensa ou Cancelar; explicar que não recupera dados ausentes. |
| W11-04 | Finalizar ficha na base | Quando pending_base_review e base_review, escolher desfecho (Não informado/Aguardando no hospital/Liberado no local pelo médico/QTA/Recusa), completar tipo de recusa se aplicável e Finalizar ficha; mostrar validação real. |
| W11-05 | Finalizar ocorrência na base | Usar Finalizar na base, conferir protocolo cliente, pedido de cópia cliente sob solicitação e cópia hospital por vítima/e-mail; em EMPTY_VICTIM_FORMS usar Revisar ficha da vítima N. |
| W11-06 | Motivo da correção | Ao salvar alteração protegida abrir Motivo da correção, conferir campos, preencher motivo 3–500 caracteres e Confirmar correção ou Cancelar. |
| W11-07 | Anular e restaurar | Em vítima elegível usar Anular/Restaurar, preencher motivo e confirmar no diálogo; filtrar Anuladas/Ativas e conferir histórico. |
| W11-08 | Emenda pós-fechamento | Com admin/coordenador e registro profissional, em concluída clicar Emendar desfecho, alterar somente campos liberados, informar motivo ao salvar e Sair da emenda. |
| W11-09 | Adendo | Em concluída e com acesso elegível preencher Texto do adendo e Motivo do adendo, Registrar adendo; conferir registro novo no histórico. |
| W11-10 | Histórico e versões | Abrir Histórico, ler operação/versão/data/motivo/autor profissional; expandir Comparar valores protegidos Antes/Depois e Carregar revisões anteriores. |
| W11-11 | Revisão de emenda | Em emenda pendente e perfil autorizado preencher Motivo da revisão; Aprovar emenda ou Rejeitar e restaurar; conferir revisão resultante. |
| W11-12 | Conflitos de gravação | Ao receber atualização/divergência de versão na ficha/equipe, preservar contexto, ler a mensagem e comparar estado do servidor antes de reaplicar; não ensinar refresh indiscriminado como resolução. |
| W11-13 | Duplicidades: filtros e comparação | Abrir Duplicidades, alternar Pendentes/Resolvidas/Descartadas/Todas, carregar mais; comparar nomes/documentos/telefone/sexo/idade e escolher ficha principal. |
| W11-14 | Duplicidades: decisão explícita | Escolher valor principal/secundário para cada campo conflitante, registrar motivo e Reconciliar; para falso alerta Descartar alerta; em resolvida elegível Desfazer reconciliação com motivo. |

Fontes: `apps/web/src/app/operacional/ocorrencias/[id]/OperationalClosurePanel.tsx`, `apps/web/src/app/operacional/ocorrencias/[id]/DeviceCheckpointList.tsx`, `apps/web/src/app/ocorrencias/[id]/BaseReviewVictimCompletionForm.tsx`, `apps/web/src/app/ocorrencias/[id]/BaseCloseOccurrenceButton.tsx`, `apps/web/src/app/operacional/ocorrencias/[id]/CorrectionReasonDialog.tsx`, `apps/web/src/app/operacional/ocorrencias/[id]/VictimLifecycleActions.tsx`, `apps/web/src/app/operacional/ocorrencias/[id]/ClinicalAddendumForm.tsx`, `apps/web/src/app/operacional/ocorrencias/[id]/ClinicalRevisionTimeline.tsx`, `apps/web/src/app/operacional/ocorrencias/[id]/VictimDuplicateReview.tsx`, `apps/web/src/lib/occurrenceAccess.ts`, `packages/regras-de-resgate/src/multi-victim.ts`.

Limitações e cuidado de gravação: Comandos de coordenação usam admin/coordenador, não org_admin. Não encenar dispensa como sincronização nem correção como apagamento da versão original. A assinatura/recusa inicial e o fechamento em campo também precisam do módulo mobile. Conflito só pode ser dito demonstrado se provocado e resolvido de verdade no runtime local.

## W12 — Solicitar cópias, acompanhar entregas e consultar auditoria

| ID | Tela/ação | Como operar e concluir |
|---|---|---|
| W12-01 | Cópia hospitalar | Em Cópias seguras selecionar vítima concluída, preencher um e-mail, selecionar responsável e usar Enviar cópia somente se disponível; demonstrar bloqueios reais localmente. |
| W12-02 | Cópia do cliente | Conferir destinatários cadastrados e Enviar cópia ao cliente; não adicionar campo redundante de endereço. |
| W12-03 | Cópia avulsa | Abrir Enviar para outro destinatário, selecionar ocorrência inteira ou vítima concluída, preencher Destinatário avulso e Enviar cópia avulsa. |
| W12-04 | Histórico de entrega | Ler destinatário mascarado, tipo, vítima, tentativas, horário e estado; distinguir solicitação registrada, aceito e entregue; reconhecer bloqueios e falhas. |
| W12-05 | Ações de entrega | Conforme allowedActions, demonstrar Cancelar/Revogar link/Tentar novamente/Retomar/Reenviar em estados locais reais; não disparar e-mail externo. |
| W12-06 | Link seguro | Explicar validade de 24 horas, revogação, ausência de anexo e possibilidade de encaminhamento enquanto válido; não expor URL/token de documento. |
| W12-07 | Auditoria: filtros | Em /configuracoes/auditoria pesquisar termo, selecionar ação/concessionária e Filtrar; limite da lista é 100 eventos recentes. |
| W12-08 | Auditoria: evento | Conferir data, ação, autor/papel, concessionária, recurso e metadados pertinentes de evento sintético, como PDF emitido; ocultar IP/identificadores sensíveis. |
| W12-09 | Configurações por papel | Em /configuracoes localizar atalhos permitidos e mostrar ausência de área plataforma nos demais perfis. |
| W12-10 | Prontidão da plataforma | Com platform:operate ler grupos e filtros Total/OK/Bloqueios/Evidências; não mostrar segredo nem tratar evidência manual como teste aprovado. |
| W12-11 | Simular acesso | Somente superadmin permitido e recurso habilitado: abrir /configuracoes/simular, escolher Usuários do painel ou Viaturas ativas e Entrar como; demonstrar em perfil separado e explicar que não é recurso cotidiano do operador. |

Fontes: `apps/web/src/entrega-de-documentos/delivery-panel.tsx`, `apps/web/src/app/configuracoes/auditoria/page.tsx`, `apps/web/src/app/configuracoes/page.tsx`, `apps/web/src/app/configuracoes/simular/page.tsx`, `apps/web/src/app/configuracoes/simular/ImpersonationPicker.tsx`.

Limitações e cuidado de gravação: Provedor de entrega indisponível localmente; mostrar bloqueio, sem fabricar aceite/entrega. canRequest do componente usa admin/coordenador/coordinator; org_admin não aparece nessa condição. Link seguro contém acesso ao documento; não imprimir URL/token no tutorial. A simulação depende de feature flag e allowlist de superadmin; não existe botão de retorno verificado na casca.

## Apêndice A — dicionário de campos do editor

A operação ensinada para cada campo é registrar informação já verificada, respeitar tipo/unidade e clicar no salvamento da área. O inventário nomeia controles clínicos para cobertura visual; não ensina diagnóstico, prescrição, triagem ou conduta. Campos derivados devem ser ajustados na origem indicada pela interface. Os formulários completos são cobertos pelos IDs W09-04 a W09-09 e W10-02 a W10-16.

### Ocorrência

- Horários: Acionamento; Saída da base; Chegada no local; Chegada no hospital; Retorno à base.
- QTH: latitude, longitude, precisão GPS em metros, momento da captura. Não há botão de captura de GPS verificado nesse formulário web.
- Rodovia: tipo estadual/federal, nome, quilômetro.
- Cena: marcadores de avaliação, tempo preso em ferragens, número de veículos e pessoas envolvidas; mecanismo; impacto; posição do veículo; proteção.
- Destino: hospital; outro destino; identificação do profissional que recebeu o caso (nome e CRM/COREN); e-mail hospitalar; solicitação de cópia.
- Cliente: protocolo e pedido de cópia; destinatário/regra configurados fora da ficha.
- Equipe: política de assinaturas (`responsible_only`, `all_team`, `optional`) e participante responsável; cadastro de participantes em Fechamento. Salvar ocorrência e Salvar participantes são comandos distintos.

### Participantes da ocorrência

Nome com busca no diretório a partir de dois caracteres; função; conselho; número profissional; responsável; assinatura. Adicionar participante, Remover participante, Limpar assinatura e Salvar participantes. Duplicidade de nome/registro e registro profissional ausente podem bloquear a gravação. Alterar função invalida a assinatura incompatível. Assinatura pertence ao signatário.

### Vítima

- Dados: Nome, Sexo M/F, Idade inteira, Placa, Tipo do veículo.
- Triag: escolha única entre as classificações oferecidas pelo catálogo de triagem; não usar o treinamento para escolher a classificação.
- Vitais: FR, PA sistólica/diastólica, FC, SpO2; respeitar rótulos e unidades.
- Glasgow: resposta ocular, verbal, motora; total derivado e AVDN; valores são avaliação registrada.
- Obs: Observações e Evolução de enfermagem.
- Desfecho: Recusa, QTA, Aguardando no hospital, Liberado no local pelo médico; recusa de atendimento/transporte condicional; indicador de assinatura da vítima registrada/obrigatória.
- Campos complementares do desfecho: rua/avenida, número, bairro, CEP, cidade, estado, telefone, RG, CPF; e-mail hospitalar e pedido de cópia; FR, PA, FC e SpO2 finais; óbito antes do atendimento/na primeira hora; data do óbito/alta.
- Assinat: lista de assinaturas existentes. Captura/substituição fica nos controles de equipe ou FAR, conforme o tipo; não inventar canvas nesta aba.
- Salvar ficha: alterações e versão atual; motivo obrigatório em correções protegidas. Aviso de atualização do servidor exige revisar o estado atual antes de gravar novamente.

### XABCDE — controles e condicionais

| Aba | Avaliação | Conduta e campos adicionais |
|---|---|---|
| X | Hemorragia externa grave | Compressão direta; Torniquete; bloco de conduta condicionado à avaliação. |
| A | Vias aéreas pérveas; Vias aéreas obstruídas (exclusivos) | Colar cervical; Aspiração; O2 máscara alta concentração; Abertura de vias aéreas; Cânula oro/nasofaríngea. |
| B | Cianose; Pneumotórax; Insuficiência respiratória; Hemotórax; Tórax instável | Intubação; Motivo da intubação aparece quando marcado. |
| C | Choque; Hemorragia; Pulso ausente | Compressão torácica; Acesso venoso; Controle de hemorragias; Perfusão com Sem avaliação/Normal/Diminuída/Ausente. |
| D | Miose; Midríase; Isocóricas; Anisocoria; Fotorreagentes; Consciente | Lado esquerdo/direito da anisocoria quando selecionada; exclusividade de estados incompatíveis. |
| E | Hipotermia; Exposição | Aquecimento; Prancha; Retirada rápida; KED. |

Fontes: `apps/web/src/lib/operationalEditorFields.ts`, `apps/web/src/app/operacional/ocorrencias/[id]/XabcdeFields.tsx`, `packages/regras-de-resgate/src/operational-xabcde.ts`, `apps/web/src/app/operacional/ocorrencias/[id]/OutcomeFields.tsx`.

## Apêndice B — estrutura completa do catálogo FAR base

Referência: `packages/regras-de-resgate/src/far.ts`, `far-runtime-catalog.ts`, `far-repeatable-lists.ts`, `operational-far.ts`, `far-derived.ts`. O catálogo publicado no banco pode variar; conferir a versão visível antes da gravação. São duas páginas e dezenove seções no catálogo base desta revisão. A tabela lista os campos declarados e as famílias repetíveis. Campo declarado não implica controle editável separado: valores promovidos/derivados, listas, assinaturas e mapas têm apresentações próprias.

### Cabeçalho da ocorrência — page_1

| Campo | Rótulo no catálogo | Tipo |
|---|---|---|
| `ambulance_type_usb` | USB | checkbox |
| `ambulance_type_usa` | USA | checkbox |
| `base_sau_bso` | Base/SAU/BSO | text |
| `occurrence_date` | Data | date |
| `occurrence_number` | Nº Ocorrência | text |

### Horários, rodovia e destino — page_1

| Campo | Rótulo no catálogo | Tipo |
|---|---|---|
| `dispatch_triggered_at` | Horário do acionamento | time |
| `occurrence_arrival_at` | Horário na ocorrência | time |
| `departed_base_at` | Horário da saída | time |
| `arrived_hospital_at` | Horário no hospital | time |
| `finished_at` | Horário do término | time |
| `returned_sau_bso_at` | Horário no SAU/BSO | time |
| `highway_state` | Rodovia estadual | checkbox |
| `highway_federal` | Rodovia federal | checkbox |
| `highway_name` | Rodovia | text |
| `highway_km` | Km | number |
| `destination_hospital` | Hospital de destino | text |
| `destination_other` | Outro destino | text |
| `destination_physician_or_nurse` | Caso passado para: nome + médico/CRM, enfermeiro/COREN ou técnico/COREN | text |

### Avaliação da cena — page_1

| Campo | Rótulo no catálogo | Tipo |
|---|---|---|
| `scene_pp` | PP | checkbox |
| `scene_fire` | Incêndio | checkbox |
| `scene_trapped_in_hardware_time` | Preso em ferrag./tempo | text |
| `scene_incarcerated` | Encarcerado | checkbox |
| `scene_ravine` | Ribanceira | checkbox |
| `scene_vehicle_count` | N° veículos | number |
| `scene_people_involved` | N° pessoas envolvidas | number |
| `scene_walking` | Deambulando | checkbox |
| `scene_clinical` | Clínico | checkbox |
| `scene_other` | Outro | text |

### Identificação do usuário — page_1

| Campo | Rótulo no catálogo | Tipo |
|---|---|---|
| `patient_name` | Nome | text |
| `patient_sex` | Sexo | select |
| `patient_age` | Idade | number |
| `patient_address_street` | Endereço da vítima · rua/avenida | text |
| `patient_address_number` | Endereço da vítima · nº | text |
| `patient_neighborhood` | Endereço da vítima · bairro | text |
| `patient_zip_code` | Endereço da vítima · CEP | text |
| `patient_city` | Endereço da vítima · cidade | text |
| `patient_state` | Endereço da vítima · UF | text |
| `patient_phone` | Telefone da vítima | text |
| `patient_rg` | RG da vítima | text |
| `patient_cpf` | CPF da vítima | text |
| `driver_license_time` | Tempo de habilitação do condutor | text |
| `patient_schooling` | Escolaridade | text |

### Informações do veículo — page_1

| Campo | Rótulo no catálogo | Tipo |
|---|---|---|
| `vehicle_plate` | Veículo da vítima · placa | text |
| `vehicle_uf` | Veículo da vítima · UF | text |
| `vehicle_type_passenger` | passeio | checkbox |
| `vehicle_type_utility` | utilitário | checkbox |
| `vehicle_type_bus` | ônibus | checkbox |
| `vehicle_type_motorcycle` | moto | checkbox |
| `vehicle_type_truck` | caminhão | checkbox |
| `vehicle_type_other` | outro | text |
| `vehicle_model` | Modelo | text |
| `vehicle_color` | Cor | text |
| `vehicle_damage_deformity` | Deformidade | checkbox |
| `vehicle_damage_steering_wheel` | volante | checkbox |
| `vehicle_damage_cabin` | cabine (>40cm) | checkbox |
| `vehicle_damage_windshield` | parabrisa | checkbox |
| `vehicle_damage_dashboard` | painel | checkbox |
| `vehicle_damage_helmet` | capacete | checkbox |

### Gravidade e apoio — page_1

| Campo | Rótulo no catálogo | Tipo |
|---|---|---|
| `triage_green_light` | Verde/Leve | checkbox |
| `triage_yellow_moderate` | Amarelo/Moderado | checkbox |
| `triage_red_severe` | Vermelho/Grave | checkbox |
| `triage_gray_black_death_evident` | Cinza - Preto/Morte Evidente | checkbox |
| `triage_white_death_physician` | Branco/Óbito constatado por médico no local | checkbox |
| `triage_no_apparent_injury` | Sem lesão aparente | checkbox |
| `triage_blue_pcr_rcp` | Azul/PCR + RCP | checkbox |
| `triage_start` | Start | checkbox |
| `support_firefighters` | C.Bombeiro | checkbox |
| `support_ambulance` | Ambulância | checkbox |
| `support_police` | P.M.Rv/P.R.F. | checkbox |
| `support_vehicle_number` | Nº da viatura de apoio | text |
| `transported_by` | Transportado por | text |

### Mecanismo de trauma — page_1

| Campo | Rótulo no catálogo | Tipo |
|---|---|---|
| `mechanism_frontal_collision` | colisão frontal | checkbox |
| `mechanism_right_side_collision` | colisão lateral D | checkbox |
| `mechanism_left_side_collision` | colisão lateral E | checkbox |
| `mechanism_rear_collision` | colidido na traseira | checkbox |
| `mechanism_rollover` | capotamento/tomb | checkbox |
| `mechanism_multiple` | multiplas | checkbox |
| `mechanism_run_off_road` | saída de pista | checkbox |
| `mechanism_run_over` | atropelado | checkbox |
| `impact_front_vehicle` | frente veic | checkbox |
| `impact_rear_vehicle` | tras. veic | checkbox |
| `impact_side_vehicle` | lateral veic | checkbox |
| `impact_barrier` | mureta | checkbox |
| `impact_embankment` | barranco | checkbox |
| `impact_tree` | árvore | checkbox |
| `impact_pedestrian` | pedestre | checkbox |
| `impact_animal` | animal | checkbox |
| `vehicle_position_driver` | motorista | checkbox |
| `vehicle_position_front_passenger` | passag. frente | checkbox |
| `vehicle_position_rear_passenger` | passageiro trás | checkbox |
| `vehicle_position_ejected` | ejetado | checkbox |
| `vehicle_position_cyclist` | ciclista | checkbox |
| `vehicle_position_motorcyclist` | motociclista | checkbox |
| `vehicle_position_passenger_garupa` | garupa | checkbox |
| `vehicle_position_other` | outro | text |
| `protection_used_yes` | Sistema de proteção utilizado: S | checkbox |
| `protection_used_no` | Sistema de proteção utilizado: N | checkbox |
| `protection_seatbelt` | cinto de segurança | checkbox |
| `protection_airbag` | air bag | checkbox |
| `protection_helmet` | capacete | checkbox |
| `protection_none` | nenhum | checkbox |
| `protection_unknown` | ignorado | checkbox |
| `protection_child_seat` | cadeirinha | checkbox |
| `incident_suspected_alcohol` | etilismo(suspeita) | checkbox |
| `incident_suspected_sleep` | sono/cansaço | checkbox |
| `incident_suspected_disease` | doença | checkbox |
| `incident_suspected_negligence` | negligência | checkbox |
| `incident_suspected_vehicle_problem` | prob. Veículo | checkbox |
| `incident_suspected_weather` | cond. atmosféricas | checkbox |
| `incident_suspected_slow_stopped_vehicle` | veíc.parado/lento | checkbox |
| `incident_suspected_unknown` | ignorado | checkbox |
| `other_fatal_victims_no` | Vítimas fatais outras: N | checkbox |
| `other_fatal_victims_yes` | Vítimas fatais outras: S | checkbox |
| `other_fatal_victims_count` | Vítimas fatais outras: Nº | number |

### Avaliação primária XABCDE e condutas — page_1

| Campo | Rótulo no catálogo | Tipo |
|---|---|---|
| `x_hemorrhage` | Hemorragia externa grave | checkbox |
| `x_direct_compression` | Compressão direta | checkbox |
| `x_tourniquet` | Torniquete | checkbox |
| `a_airway_patent` | Vias Aéreas Pérveas | checkbox |
| `a_airway_obstructed` | Vias Aéreas Obstruídas | checkbox |
| `a_cervical_collar` | Colar Cervical | checkbox |
| `a_aspiration` | Aspiração | checkbox |
| `a_o2_high_concentration_mask` | O2 Máscara Alta Conc. | checkbox |
| `a_airway_opening` | Abertura Vias Aéreas | checkbox |
| `a_oro_nasopharyngeal_cannula` | Cânula Oro/NasoFaringea | checkbox |
| `b_cyanosis` | Cianose | checkbox |
| `b_pneumothorax` | pneumotór. | checkbox |
| `b_flail_chest` | tórax instável | checkbox |
| `b_resp_failure` | Insuf.Resp. | checkbox |
| `b_hemothorax` | hemotórax | checkbox |
| `b_intubation` | Intubação | checkbox |
| `b_intubation_reason` | Motivo da intubação | text |
| `b_permeabilization` | Permeabilização | checkbox |
| `b_thoracentesis` | Toracocentese | checkbox |
| `b_cricostomy` | Cricostomia | checkbox |
| `b_resp_failure_conduct` | Insuf. Respiratória | checkbox |
| `b_ecg_less_equal_8` | ECG< ou = 8 | checkbox |
| `c_perfusion_normal` | perfusão: Normal <2s | checkbox |
| `c_perfusion_decreased` | Diminuida >2s | checkbox |
| `c_perfusion_absent` | Ausente | checkbox |
| `c_shock` | Choque | checkbox |
| `c_hemorrhage` | hemorragia | checkbox |
| `c_pulse_absent` | Pulso ausente | checkbox |
| `c_chest_compression` | Compressão torácica | checkbox |
| `c_venous_access` | Acesso venoso | checkbox |
| `c_defibrillation` | Desfibrilação | checkbox |
| `c_hemorrhage_control` | Controle de Hemorragias | checkbox |
| `d_pupils_miosis` | PUPILAS miose | checkbox |
| `d_pupils_isocoric` | Isocóricas | checkbox |
| `d_pupils_photoreactive` | Fotorreagentes | checkbox |
| `d_pupils_mydriasis` | midriase | checkbox |
| `d_pupils_anisocoria` | Anisocoria (D E) | checkbox |
| `d_conscious_yes` | Consciente: S | checkbox |
| `d_conscious_no` | Consciente: N | checkbox |
| `e_hypothermia` | Hipotermia | checkbox |
| `e_exposure` | Exposição | checkbox |
| `e_warming` | Aquecimento | checkbox |
| `e_rapid_extrication` | Retirada rápida | checkbox |
| `e_backboard` | Prancha | checkbox |
| `e_ked` | KED | checkbox |

### Sinais vitais — page_1

| Campo | Rótulo no catálogo | Tipo |
|---|---|---|
| `initial_vital_fr` | Inicial: FR | number |
| `initial_vital_pa` | Inicial: PA | text |
| `initial_vital_fc` | Inicial: FC | number |
| `initial_vital_spo2` | Inicial: Sat O2 | number |
| `final_vital_fr` | Final: FR | number |
| `final_vital_pa` | Final: PA | text |
| `final_vital_fc` | Final: FC | number |
| `final_vital_spo2` | Final: Sat O2 | number |

### Avaliação secundária — page_1

| Campo | Rótulo no catálogo | Tipo |
|---|---|---|
| `regulating_physician` | Médico regulador primário | text |
| `regulating_physician_crm` | CRM primário | text |
| `regulating_physician_secondary` | Médico regulador secundário | text |
| `regulating_physician_secondary_crm` | CRM secundário | text |
| `secondary_injury_amputation_avulsion` | 1- Amputação/avulsão | checkbox |
| `secondary_injury_contusion` | 2- Contusão | checkbox |
| `secondary_injury_cut` | 3- Corte | checkbox |
| `secondary_injury_laceration` | 4- Laceração | checkbox |
| `secondary_injury_abrasion` | 5- Escoriação | checkbox |
| `secondary_injury_firearm` | 6- Arma de fogo | checkbox |
| `secondary_injury_knife` | 7- Arma branca | checkbox |
| `secondary_injury_fracture_open` | 8- Fratura exposta | checkbox |
| `secondary_injury_fracture_closed_suspected` | 9- Fratura fechada (suspeita) | checkbox |
| `secondary_injury_perforating` | 10- Perfurante | checkbox |
| `secondary_injury_burn` | 11- Queimadura | checkbox |
| `secondary_injury_other` | 12- Outros | text |
| `secondary_burn_assessment` | Avaliação da queimadura | burn_assessment |
| `secondary_body_map` | Diagrama corporal (localização da lesão) | body_map |

### SAMPLA, observações e evolução — page_1

| Campo | Rótulo no catálogo | Tipo |
|---|---|---|
| `sampla_observations_text` | SAMPLA e Observações | textarea |
| `nursing_evolution_text` | Evolução da Enfermagem | textarea |
| `use_back_if_needed` | Utilize o verso se necessário | textarea |

### Êxito e termo de responsabilidade — page_1

| Campo | Rótulo no catálogo | Tipo |
|---|---|---|
| `outcome_refusal` | recusa | checkbox |
| `outcome_qta` | QTA | checkbox |
| `outcome_waiting_hospital` | aguardando no hospital | checkbox |
| `outcome_released_by_physician` | liberado no local por médico | checkbox |
| `outcome_death_before_care` | Óbito antes atend. | checkbox |
| `outcome_death_first_hour` | Óbito na 1ª hora | checkbox |
| `death_date` | Dia do Óbito | date |
| `death_time` | Hora do Óbito | time |
| `discharge_date` | Alta dia | date |
| `refusal_client_name` | Cliente | text |
| `refusal_client_rg` | RG | text |
| `refusal_attendance` | Recusa atendimento por esta unidade | checkbox |
| `refusal_transport` | Recusa transporte por esta ambulância | checkbox |
| `refusal_medical_report` | Relatório da equipe médica — orientação prestada | textarea |
| `refusal_rescuer_report` | Relatório do socorrista/enfermeiro — orientação prestada | textarea |
| `refusal_date` | Data | date |
| `refusal_signature` | Assinatura | signature |

### Escala de Coma de Glasgow — page_2

| Campo | Rótulo no catálogo | Tipo |
|---|---|---|
| `adult_glasgow_eye_spontaneous` | Abertura ocular adulto: Espontânea | checkbox |
| `adult_glasgow_eye_verbal_request` | Abertura ocular adulto: Solicitação verbal | checkbox |
| `adult_glasgow_eye_pain` | Abertura ocular adulto: Estímulo doloroso | checkbox |
| `adult_glasgow_eye_none` | Abertura ocular adulto: Nenhuma | checkbox |
| `adult_glasgow_verbal_oriented` | Resposta verbal adulto: Orientado | checkbox |
| `adult_glasgow_verbal_confused` | Resposta verbal adulto: Confuso | checkbox |
| `adult_glasgow_verbal_inappropriate_words` | Resposta verbal adulto: Palavras inapropriadas | checkbox |
| `adult_glasgow_verbal_sounds_moans` | Resposta verbal adulto: Sons ou gemidos | checkbox |
| `adult_glasgow_verbal_none` | Resposta verbal adulto: Nenhuma | checkbox |
| `adult_glasgow_motor_obeys` | Resposta motora adulto: Obedece à ordens | checkbox |
| `adult_glasgow_motor_localizes_pain` | Resposta motora adulto: Localiza a dor | checkbox |
| `adult_glasgow_motor_withdraws` | Resposta motora adulto: Retira aos estímulos | checkbox |
| `adult_glasgow_motor_abnormal_flexion` | Resposta motora adulto: Flexão anormal | checkbox |
| `adult_glasgow_motor_abnormal_extension` | Resposta motora adulto: Extensão anormal | checkbox |
| `adult_glasgow_motor_none` | Resposta motora adulto: Nenhuma | checkbox |
| `child_glasgow_eye_spontaneous` | Abertura ocular criança: Espontânea | checkbox |
| `child_glasgow_eye_verbal_request` | Abertura ocular criança: Solicitação verbal | checkbox |
| `child_glasgow_eye_pain` | Abertura ocular criança: Estímulo doloroso | checkbox |
| `child_glasgow_eye_none` | Abertura ocular criança: Nenhuma | checkbox |
| `child_glasgow_verbal_talks` | Resposta verbal criança: Fala | checkbox |
| `child_glasgow_verbal_irritated_cry` | Resposta verbal criança: Choro Irrritado | checkbox |
| `child_glasgow_verbal_pain_cry` | Resposta verbal criança: Chora à estimulação dolor. | checkbox |
| `child_glasgow_verbal_pain_moan` | Resposta verbal criança: Geme à estimulação dolor. | checkbox |
| `child_glasgow_verbal_none` | Resposta verbal criança: Nenhuma | checkbox |
| `child_glasgow_motor_spontaneous` | Resposta motora criança: Movimentação espontânea | checkbox |
| `child_glasgow_motor_localizes_pain` | Resposta motora criança: Localiza a dor | checkbox |
| `child_glasgow_motor_withdraws` | Resposta motora criança: Retira aos estímulos | checkbox |
| `child_glasgow_motor_abnormal_flexion` | Resposta motora criança: Flexão anormal | checkbox |
| `child_glasgow_motor_abnormal_extension` | Resposta motora criança: Extensão anormal | checkbox |
| `child_glasgow_motor_none` | Resposta motora criança: Nenhuma | checkbox |

### Escalas de trauma e AVDN — page_2

| Campo | Rótulo no catálogo | Tipo |
|---|---|---|
| `adult_trauma_glasgow_13_15` | Trauma adulto Glasgow: 13 a 15 | checkbox |
| `adult_trauma_glasgow_9_12` | Trauma adulto Glasgow: 9 a 12 | checkbox |
| `adult_trauma_glasgow_6_8` | Trauma adulto Glasgow: 6 a 8 | checkbox |
| `adult_trauma_glasgow_4_5` | Trauma adulto Glasgow: 4 a 5 | checkbox |
| `adult_trauma_glasgow_3` | Trauma adulto Glasgow: 3 | checkbox |
| `adult_trauma_sbp_gt_89` | Trauma adulto PA Sistólica: >89 | checkbox |
| `adult_trauma_sbp_76_89` | Trauma adulto PA Sistólica: 76 a 89 | checkbox |
| `adult_trauma_sbp_50_75` | Trauma adulto PA Sistólica: 50 a 75 | checkbox |
| `adult_trauma_sbp_1_49` | Trauma adulto PA Sistólica: 1 a 49 | checkbox |
| `adult_trauma_sbp_0` | Trauma adulto PA Sistólica: 0 | checkbox |
| `adult_trauma_fr_10_29` | Trauma adulto Freq. Respiratória: 10 a 29 | checkbox |
| `adult_trauma_fr_gt_29` | Trauma adulto Freq. Respiratória: >29 | checkbox |
| `adult_trauma_fr_6_9` | Trauma adulto Freq. Respiratória: 6 a 9 | checkbox |
| `adult_trauma_fr_1_5` | Trauma adulto Freq. Respiratória: 1 a 5 | checkbox |
| `adult_trauma_fr_0` | Trauma adulto Freq. Respiratória: 0 | checkbox |
| `avdn_alert` | AVDN: Paciente está alerta | checkbox |
| `avdn_verbal` | AVDN: Responde a estimulação verbal | checkbox |
| `avdn_pain` | AVDN: Responde a estímulos dolorosos | checkbox |
| `avdn_none` | AVDN: Não responde | checkbox |
| `pediatric_trauma_weight_gt_20kg` | Trauma pediátrico peso: > 20 kg | checkbox |
| `pediatric_trauma_weight_11_20kg` | Trauma pediátrico peso: 11-20 kg | checkbox |
| `pediatric_trauma_weight_lt_10kg` | Trauma pediátrico peso: < 10kg | checkbox |
| `pediatric_trauma_airway_normal` | Trauma pediátrico vias aéreas: normal | checkbox |
| `pediatric_trauma_airway_mask_guedel` | Trauma pediátrico vias aéreas: máscara, guedel | checkbox |
| `pediatric_trauma_airway_intubated` | Trauma pediátrico vias aéreas: intubado, cricot. | checkbox |
| `pediatric_trauma_sbp_gt_90` | Trauma pediátrico PA Sistólica: > 90 | checkbox |
| `pediatric_trauma_sbp_50_90` | Trauma pediátrico PA Sistólica: 50-90 | checkbox |
| `pediatric_trauma_sbp_lt_50` | Trauma pediátrico PA Sistólica: < 50 | checkbox |
| `pediatric_trauma_perfusion_good` | Trauma pediátrico perfusão: boa perfusão | checkbox |
| `pediatric_trauma_perfusion_no_peripheral_pulse` | Trauma pediátrico perfusão: pulso perifir. não palpável | checkbox |
| `pediatric_trauma_perfusion_no_pulse` | Trauma pediátrico perfusão: sem pulso | checkbox |
| `pediatric_trauma_conscious_awake` | Trauma pediátrico consciência: desperto | checkbox |
| `pediatric_trauma_conscious_torporous` | Trauma pediátrico consciência: torporoso | checkbox |
| `pediatric_trauma_conscious_comatose` | Trauma pediátrico consciência: comatoso | checkbox |
| `pediatric_trauma_skin_no_injury` | Trauma pediátrico lesão cutânea: sem lesão | checkbox |
| `pediatric_trauma_skin_closed_or_small_rupture` | Trauma pediátrico lesão cutânea: lesões fechadas ou ruptura < 7cm | checkbox |
| `pediatric_trauma_skin_large_or_multiple` | Trauma pediátrico lesão cutânea: laceração > 7cm ou múltip. lesões | checkbox |
| `pediatric_trauma_fracture_absent` | Trauma pediátrico fratura: ausente | checkbox |
| `pediatric_trauma_fracture_single_closed` | Trauma pediátrico fratura: única e fechada | checkbox |
| `pediatric_trauma_fracture_open_multiple` | Trauma pediátrico fratura: aberta e múltiplas | checkbox |

### Diagnóstico de enfermagem — page_2

| Campo | Rótulo no catálogo | Tipo |
|---|---|---|
| `nursing_diagnosis_airway_clearance_ineffective` | 31 - Desobstrução ineficaz de vias aéreas | checkbox |
| `nursing_diagnosis_risk_for_aspiration` | 39 - Risco de aspiração | checkbox |
| `nursing_diagnosis_ineffective_breathing_pattern` | 32 - Padrão ventilatório Ineficaz | checkbox |
| `nursing_diagnosis_deficient_fluid_volume` | 27 - Volume de líquido deficiente | checkbox |
| `nursing_diagnosis_ineffective_tissue_perfusion` | 24 - Perfussão tissular Ineficaz | checkbox |
| `nursing_diagnosis_impaired_sensory_perception` | 122 - Percepção sensorial prejudicada | checkbox |
| `nursing_diagnosis_acute_confusion` | 128 - Confusão aguda | checkbox |
| `nursing_diagnosis_temperature_risk` | 5 - Risco p/ temp corporal desequilibrada | checkbox |
| `nursing_diagnosis_neurovascular_risk` | 86 - Risco para disfunção neurovascular periférica | checkbox |
| `nursing_diagnosis_acute_pain` | 132 - Dor aguda | checkbox |
| `nursing_diagnosis_impaired_skin_integrity` | 46 - Integridade de pele prejudicada | checkbox |
| `nursing_diagnosis_impaired_tissue_integrity` | 44 - Integridadde tissular prejudicada | checkbox |
| `nursing_diagnosis_impaired_physical_mobility` | 85 - Mobilidade física prejudicada | checkbox |
| `nursing_diagnosis_risk_for_trauma` | 38 - Risco para trauma | checkbox |
| `nursing_diagnosis_anxiety` | 146 - Ansiedade | checkbox |
| `nursing_diagnosis_fear` | 148 - Medo | checkbox |
| `nursing_diagnosis_self_violence_risk` | 140 - Risco para violência autoinflingida | checkbox |
| `nursing_diagnosis_impaired_verbal_communication` | 51 - Comunicação verbal prejudicada | checkbox |
| `nursing_diagnosis_infection_risk` | 4 - Risco de infecção | checkbox |

### Drogas e materiais utilizados — page_2

| Campo | Rótulo no catálogo | Tipo |
|---|---|---|
| `material_item_{1..10}_nature/description/quantity/unit/origin` | Natureza, Descrição, Qtde., Unidade; Origem armazenada, sem controle direto | lista de até 10 linhas |

### Receita de psicotrópicos — page_2

| Campo | Rótulo no catálogo | Tipo |
|---|---|---|
| `psychotropic_patient_name` | NOME DO PACIENTE | text |
| `psychotropic_patient_cpf` | CPF | text |
| `psychotropic_patient_address` | ENDEREÇO | text |
| `psychotropic_patient_city_uf` | CIDADE/UF | text |
| `psychotropic_physician_signature_stamp` | Médico (assinatura e carimbo) | signature |
| `psychotropic_item_{1..10}_drug/validity/quantity/lot` | Droga, Validade, Qtde., Lote | lista de até 10 linhas |

### Recolhimento de bens e valores — page_2

| Campo | Rótulo no catálogo | Tipo |
|---|---|---|
| `belongings_collected_by_name` | Recolhido por | text |
| `belongings_collected_by_rg_cpf` | RG/CPF recolhido por | text |
| `belongings_collected_by_signature` | Assinatura recolhido por | signature |
| `belongings_receiver_name` | Recebido por | text |
| `belongings_receiver_rg_cpf` | RG/CPF recebido por | text |
| `belongings_receiver_signature` | Assinatura recebido por | signature |
| `belongings_item_{1..7}` e sufixos `_description/_quantity/_notes` | Item, Discriminação, Qtde., Obs. | lista de até 7 linhas |

### Equipe legível — page_2

| Campo | Rótulo no catálogo | Tipo |
|---|---|---|
| `team_paramedic_1_signature` | Socorrista | signature |
| `team_paramedic_2_signature` | Socorrista | signature |
| `team_paramedic_3_signature` | Socorrista | signature |
| `team_nurse_tech_coren_stamp` | Aux/Téc. Enfermagem - COREN/carimbo | signature |
| `team_nurse_coren_stamp` | Enfermeiro - COREN/carimbo | signature |
| `team_physician_crm_stamp` | Médico - CRM/carimbo | signature |

Campo adicional definido por constante no catálogo: `REFUSAL_TERM_TEXT_ID` (`refusal_term_text`) — Texto do termo de recusa, textarea, seção Êxito e termo de responsabilidade.

### Controles FAR especiais e limites

- Tipos: texto, área de texto, número, seleção, checkbox, data, hora, data/hora, assinatura, mapa corporal e avaliação de queimaduras. Data/hora inválida mostra erro; campos sem valor não devem receber preenchimento inventado.
- Grupos de mecanismo/proteção/posição e suspeita têm exclusividade ou múltipla seleção conforme regra. Outros detalhes só aparecem quando o grupo pai exige. Outras vítimas fatais abre a quantidade.
- Materiais: seleção por catálogo e por bolsa/maleta; busca por nome; escolha de categoria; mais bolsas em diálogo; itens já registrados identificados; soma de selecionados não pode superar linhas livres; confirmar Adicionar à ficha e depois Salvar FAR.
- Drogas/materiais: natureza, descrição, quantidade, unidade. Origem do catálogo é preservada sem controle de edição. Receita: droga, validade, quantidade, lote, identificação e assinatura do médico participante. Bens: item, discriminação, quantidade, observações; recolhido/recebido por com identificação e assinatura.
- Assinatura: ver existente; substituir; nome/registro; participante médico obrigatório onde exigido; desenhar, limpar, Salvar assinatura, Cancelar substituição. Estado histórico pode ser apenas consultável.
- Mapa corporal: frente/verso e regiões; versão antiga permite regiões; marca estruturada é projeção de leitura no web, com tipo/região/lado/localização. Desenho anatômico e nova marca detalhada pertencem ao mobile.
- Queimaduras: indicador, método Wallace/Lund-Browder, compatibilidade de idade, anterior/posterior, região, profundidade, cobertura inteira/unidades palmares, quantidade de unidades e percentual calculado. A UI web de Lund-Browder pede localização no aplicativo antes de adicionar região detalhada. Limites/avisos são apresentados pela regra, sem escolha clínica sugerida pelo vídeo.
- Histórico de queimadura: iniciar avaliação nova pede confirmação; trocar método ou limpar avaliação também pode exigir confirmação. Preservar dados enquanto a decisão não estiver confirmada. **Não instruir remoção por segundo toque no mapa legado/Wallace**: há toggle no código, enquanto `GLOBAL_TRUTH.md` exige remoção explícita. A gravação deve registrar a limitação, não normalizá-la.

## Apêndice C — importações, modais e estados

### Contratos de importação

| Recurso | Entrada | Conclusão | Campos |
|---|---|---|---|
| Viaturas | CSV UTF-8 com `;`, arquivo ou texto, até 1.000 linhas | Importar CSV grava válidos; duplicados pulados; inválidos mostram linha/motivo | prefixo, nome, placa, tipo, base, status, login, senha, authAtiva |
| Bases | Mesmo CSV | Mesmo resultado; retirada do catálogo-modelo é opção adicional após carga válida | base, concessionaria, nome, codigo, rodovia, kmInicial, kmFinal, sentido, endereco, uf, ativo |
| Participantes | Mesmo CSV | Mesmo resultado | nome, concessionaria, funcao, registro, tipoRegistro |
| Clientes | Mesmo CSV; vários e-mails separados por `|` | Mesmo resultado | concessionaria, nome, emails, regraEnvio (`on_request`/`always_on_close`), ativo |
| Materiais | CSV/XLSX | Prévia de válidos/repetidos/erros, Confirmar importação ou Cancelar | nome, tipo, unidade opcional |

Não mostrar a coluna de senhas no vídeo. Endereço/UF da carga de bases são preservados em observações de cobertura. Repetir carga não é mecanismo de atualização dos cadastros existentes.

### Diálogos, confirmações e áreas expansíveis

- Anexo ampliado: abrir miniatura, fechar explicitamente ou pelo comportamento acessível do diálogo.
- Motivo da correção: campos alterados, motivo de 3–500 caracteres, Cancelar/Confirmar correção.
- Anular/restaurar: motivo, Cancelar inicial, confirmação explícita; depende de estado/versão/permissão.
- Mais bolsas: categorias, busca, seleção e adição à ficha.
- Remoção de logo da concessionária: confirmação explícita; logo do cliente usa controle de remoção seguido de salvar.
- Trocar método, limpar ou substituir registro histórico de queimadura: confirmações explícitas quando há dados a descartar.
- Dispensa de aparelho: formulário inline com motivo 3–500, Confirmar dispensa/Cancelar.
- Emenda: modo com campos liberados e bloqueados, aviso de registro profissional e saída do modo.
- Histórico: expansão Antes/Depois e paginação; revisão de emenda com motivo e Aprovar/Rejeitar e restaurar.
- Duplicidades: filtros Pendentes/Resolvidas/Descartadas/Todas; ficha principal; decisão por campo; motivo; Reconciliar/Descartar/Desfazer; paginação.
- Importação de materiais: prévia e confirmação; importações dos outros recursos não têm essa prévia.

### Estados que não podem ser confundidos

| Área | Estados/comportamentos |
|---|---|
| Ocorrência | Ativa, fechando, pendente de revisão de base, concluída; rótulos na lista/editor; somente leitura e emenda são modos de edição. |
| Vítima | Ativa, anulada, reconciliada; situação de preenchimento/finalização é informação adicional. |
| Aparelho | Pendente, Sincronizado, Dispensado; fila pode estar pendente/sincronizada/erro. Dispensa não recupera dado. |
| Voz | Pendente de upload/transcrição/revisão, revisado, falha, sem fala reconhecível/não publicado; arquivo ausente pede sincronizar origem. |
| OCR | Documento pendente de processamento; extraído/sem campos; sugestões válidas/conflictantes; revisão explícita. |
| Entrega | Na fila, Preparando, Enviando, Aguardando nova tentativa, Aceito, Entregue, Falha no envio, Bloqueado, Devolvido, Marcado como reclamação, Cancelado. |
| Ações de entrega | Cancelar, Revogar link, Tentar novamente, Retomar, Reenviar, conforme allowedActions. Infraestrutura indisponível bloqueia solicitações/retentativas; ações locais elegíveis continuam condicionais. |
| Prontidão | OK, Bloqueio e Evidência manual; filtro Total não é aprovação global. |

## Apêndice D — cobertura pendente e interfaces fora da rota

- OCR/transcrição: provedor não configurado no runtime local informado pela coordenação. Gravar controles/indisponibilidade; revisão de um resultado só é execução demonstrada quando há resultado real. Não semear resultado para aparentar processamento.
- Entregas: provedor local indisponível; controles e bloqueios podem ser ensinados, mas envio/aceite/entrega externos não foram demonstrados por esta tarefa.
- `apps/web/src/entrega-de-documentos/delivery-health-panel.tsx` contém painel de saúde de fila/provedor (idades, bloqueios, falhas, tentativas, percentis, webhooks, downloads, expiração e revogação). Nenhuma montagem em rota de página foi encontrada na busca de referências: inventariado como componente existente, **não** anunciado como tela alcançável pelo menu. Endpoint técnico não é substituto de uma tela para novo operador.
- Captura de mídia/GPS/áudio no dispositivo, fila offline local, primeira assinatura/termo em campo, atualização do app, configuração do aparelho e fechamento em campo dependem do módulo mobile. A existência do editor web não comprova teste nativo/hardware.
- A simulação de acesso é restrita à plataforma e configuração habilitada. Não foi localizado botão de retorno de simulação na casca; não narrar um comando inexistente.
- A casca do painel não oferece botão de logout ou recuperação de senha verificado. Sair existe na moldura operacional da ambulância.
- Conflitos de snapshot/revisão/equipe, reconciliação de duplicidades, adendo e revisão de emenda exigem cenários locais próprios. Fonte auditada não equivale a resultado filmado.
- Os checkpoints atuais W02–W08 foram consultados apenas para ordem narrativa. Não afirmam cobertura integral dos 116 IDs. Antes de anunciar 100%, cruzar cada ID com captura/trecho real ou explicação explícita da indisponibilidade; incluir as telas condicionais do catálogo efetivamente carregado.

## Verificação dos artefatos e correção necessária à gravação

JSON conferido: 12 capítulos, 116 IDs únicos, todos presentes neste inventário; fontes locais existentes; narração com 200–300 palavras por capítulo. Durações são alvos de produção. O arquivo oferece `narration` completo e `narrationSegments` por parágrafo, além dos checkpoints recebidos, sem URLs ou identificadores da ocorrência.

Durante a gravação, a coordenação encontrou HTTP 401 nas rotas do editor acessado pelo painel. A correção autorizada em `apps/web/src/proxy.ts` encaminha sessões com `occurrences:review` aos guards existentes de revisões, equipe, duplicidades, leitura paginada de vítimas e consulta de participantes. A criação continua exclusiva da viatura; autorização/tenant/versão continuam nos handlers. Regressão em `apps/web/test/proxy.test.ts`: sete cenários reproduziram 401 antes do ajuste; após ele, seis suítes passaram, com 112 testes incluindo autorização, revisão, equipe, duplicidades e participantes. Validação runtime da gravação fica com a coordenação.

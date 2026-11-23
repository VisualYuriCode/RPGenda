# RPGenda — fluxo de telas e protótipo

Protótipo revisado em 26/10/2026. Referência: página `Telas front-end` (`307:5581`) do arquivo Figma `weylcyY14ApShovzlavwJc`. As verificações do ambiente descritas adiante são de 21/10/2026 e não foram repetidas nesta edição.

O fluxo abaixo descreve o protótipo atual. A implementação local de 27/10/2026 está registrada a seguir; agenda, XP e validação da sincronização conectada continuam pendentes. Por orientação do usuário, cards e controles repetidos são exemplos do mesmo comportamento: não é necessário conectar cada repetição para definir o fluxo do produto.

## Telas encontradas

### Implementação das dez telas — 27/10/2026

**Novo APK compilado em 27/10/2026 às 11:04 (São Paulo):**
`wger-flutter/build/app/outputs/flutter-apk/app-debug.apk`, ARM64,
130,76 MiB, pacote `de.wger.flutter.debug`, versão 2.1.0 (270).
Contém as dez telas e usa `WGER_LOCAL_MODE=true`. ZIP, assinatura APK v2,
arquitetura e presença das fontes/entrada RPGenda foram verificados.
SHA-256: `3821c82f210bbea81c27d2d0fd5407b0610473f61b2a8f6a5d97e28295ba4988`.
Os metadados e checksum ficam ao lado do APK. Instalado no Motorola Edge 60 Fusion em 27/10/2026 às 11:06: versão de teste anterior removida por solicitação do usuário, nova instalação confirmada e abertura com `Status: ok`. O pacote normal `de.wger.flutter` foi preservado. A captura de verificação mostrou a nova tela de criação de quest; a consulta de abertura não encontrou erros AndroidRuntime/flutter.
O build usou Java 21, cache Gradle temporário e instrumentação interna do Gradle
desativada para contornar falhas no cache anterior; os fontes do app não precisaram
ser alterados para esta compilação.

O código agora abre `RpgendaApp` como entrada principal após a autenticação
(ou diretamente no modo local já existente). As dez telas da tabela abaixo
estão implementadas em `wger-flutter/lib/features/rpgenda/screens/`, com as
fontes GrapeSoda/Mokgech fornecidas pelo usuário e assets exportados do Figma.
As notas sobre adaptação parcial e o APK nas seções anteriores da implementação
registram etapas históricas; o APK instalado anteriormente não contém esta versão.

- Quest representa um `Day` dentro de uma rotina Wger, conforme decisão do usuário.
- Criação/edição de quest, seleção múltipla com busca e filtros Todos/Criados/Padrão,
  criação/edição de exercícios, remoção/reordenação e prescrições por quest funcionam
  no modo local. Cancelar alterações pede confirmação.
- A confirmação inicia uma sessão. Séries permitem alterar repetições/carga,
  marcar/desmarcar e adicionar séries. Finalizar com pendências exige confirmação;
  é necessário ter pelo menos uma série concluída.
- Quests, exercícios personalizados, sessão ativa e histórico são persistidos
  com SharedPreferences em registros separados por modo/conta. O treino ativo
  pode ser retomado na Home. Rascunhos de formulário permanecem na navegação,
  mas não são restaurados após encerrar o processo.
- O modo local padrão dispensa rede/login/PowerSync. Seus registros não são enviados
  ao backend. O adaptador conectado reutiliza serviços de rotina/dia/slot/configuração
  e os repositórios Drift de sessões/logs. Sua operação real com servidor e PowerSync
  permanece sem validação; criação de exercícios depende de permissão da conta.
- Decisões provisórias: Home usa a quest escolhida ou a primeira disponível;
  sessão ativa tem prioridade. O contador indica sessões concluídas hoje, com meta
  visual de uma. Mochila abre histórico; engrenagem abre configurações/catálogo.
  Esses destinos auxiliares não tinham telas próprias no escopo.
- Nome/vidas/insígnia são apresentação provisória. Agenda, descanso automático,
  XP, nível e recompensas continuam aguardando regras de produto.

Validação: testes do módulo cobrem o percurso das dez telas, configuração por quest,
persistência após reabertura, falha de gravação sem perda do estado anterior,
cancelamento de alterações e catálogo em largura de 320 px. As capturas de revisão
estão em `wger-flutter/docs/rpgenda/screenshots/`. O bundle Flutter foi compilado;
esta etapa não instalou nem alterou o aplicativo do celular.

| Nº | Tela funcional | Nó Figma | Localização |
| --- | --- | --- | --- |
| 1 | Abertura com logo | `307:5701` | Section 3, acima das telas principais |
| 2 | Home / quest de hoje | `427:9203` | Telas principais, à esquerda |
| 3 | Minhas quests | `409:4599` | Telas principais, à direita |
| 4 | Confirmar / aceitar quest | `597:4625` | Frame 34, aberto como overlay pela Home |
| 5 | Treino ativo | `427:4880` | tela - Inicio treino, à direita |
| 6 | Criando quest | `418:4937` | Tela - criar treino completo, à esquerda |
| 7 | Criando exercício | `419:4064` | Tela - criar treino completo |
| 8 | Todos os Exercícios | `420:5053` | Tela - criar treino completo |
| 9 | Editar quest | `628:4291` | Aberta pelo card de exemplo de Minhas quests |
| 10 | Editar exercício | `634:5094` | Aberta pelo item de exemplo do catálogo |

A confirmação utilizada pelo fluxo agora possui frame próprio (`597:4625`). As composições anteriores e o frame avulso `402:5810` foram preservados como referências. Há três referências ocultas fora do fluxo principal.

O arquivo contém reações de navegação e estados interativos. Além das oito telas iniciais, foram acrescentadas edição de quest e edição de exercício.

## Fluxo configurado no Figma

1. Abertura → Home após 2 segundos. A autenticação real ainda deve ser integrada ao Wger.
2. Home → aceitar quest → confirmação → treino ativo.
3. Confirmação → trocar treino → Minhas quests → selecionar outra quest.
4. Minhas quests → botão + → Criando quest.
5. Criando quest → Adicionar → catálogo de exercícios.
6. Catálogo → primeiro item alterna seleção e contador entre 0 e 1. Outro item representa a edição de exercício.
7. Catálogo → Adicionar → Criando exercício; salvar, voltar e cancelar retornam ao catálogo.
8. Catálogo → Adicionar selecionados, voltar ou cancelar → retorna à criação ou edição da quest conforme a origem. A variável `navegacao/editando-quest` preserva esse contexto mesmo ao visitar criação/edição de exercício.
9. Criando quest → salvar → Minhas quests.
10. Minhas quests → card de exemplo → Editar quest; Salvar, voltar e cancelar retornam a Minhas quests.
11. Treino ativo → checks alternam o estado visual de cada série no exercício de exemplo. “Cabei” marca as séries desse exercício como concluídas e muda o rótulo para “Concluído”. Isso é uma demonstração, não uma regra confirmada de finalização com séries pendentes.
12. Treino ativo → + revela uma quarta série de exemplo. O aplicativo deverá permitir a quantidade definida pelas regras do produto; o protótipo demonstra uma adição.
13. Treino ativo → Acabeii → Home. Ao iniciar ou encerrar, os estados de demonstração das séries são reiniciados. O protótipo não grava uma sessão real.

### Validação da edição de 26/10

As reações foram relidas após a edição, incluindo os dois destinos condicionais do catálogo e o destino de Salvar quest. Foram inspecionadas capturas do catálogo e do treino com três e quatro séries. A quarta linha ocupa o espaço esperado no auto-layout; a altura do exercício cresce de 459 para 517 px. Os valores padrão foram restaurados após a inspeção.

Não foi realizada uma sessão manual de cliques no modo Apresentar. A seleção do catálogo e a conclusão de séries são estados demonstrativos; não atualizam dados do backend nem definem se a prescrição de um exercício vale apenas para uma quest ou para outras quests.

## Relação com o código atual

- Entrada e navegação: `wger-flutter/lib/main.dart`, `lib/core/home_tabs_screen.dart`, `lib/core/splash_screen.dart`.
- Home atual: `lib/core/dashboard.dart`.
- Treinos: `lib/features/routines/screens/` e `providers/routines_repository.dart`.
- Execução: `lib/features/routines/screens/gym_mode.dart` e providers `gym_state_notifier`, `gym_log_notifier`, `workout_session_notifier`.
- Exercícios: `lib/features/exercises/`, incluindo catálogo e criação existentes.
- Armazenamento e sincronização: Riverpod, Drift/SQLite e PowerSync já integram esta versão. Não adicionar Hive antes de avaliar a estrutura existente.
- O repositório de logs grava localmente. A criação de rotina, dia, slot e configuração usa métodos de servidor; a submissão de exercícios também. Offline completo para criação exigiria trabalho adicional.
- Backend: modelos `Routine`, `Day`, `Slot`, `SlotEntry`, configurações de séries/repetições/carga, `WorkoutSession` e `WorkoutLog`.
- Endpoints confirmados no código: `/api/v2/routine/`, `/api/v2/workoutsession/`, `/api/v2/workoutlog/`, `/api/v2/exercise/`, `/api/v2/exerciseinfo/` e `/api/v2/exercise-submission/`. O `/log/` do relatório não corresponde à rota registrada para logs nesta versão.
- Há sistema de troféus e estatísticas. Isso não define automaticamente as regras de vidas, XP, nível e recompensas do RPGenda.

Decisão confirmada pelo usuário em 26/10/2026: **uma quest representa um dia de treino (`Day`) dentro de uma rotina (`Routine`) do Wger**. Séries, repetições e carga são prescrições da participação do exercício naquela quest; não devem virar propriedades globais do exercício de catálogo. As regras de agenda e repetição da quest ainda precisam ser definidas.

## Início da implementação Flutter — 26/10/2026

- A abertura agora usa o logo original exportado do Figma, armazenado no aplicativo. A saída da abertura continua sendo controlada pela autenticação; o temporizador do protótipo não substitui a verificação de credenciais.
- Foi criado o módulo `wger-flutter/lib/features/rpgenda/`, com tema e botões reutilizáveis.
- A rota existente de exercícios (`/exercises`) abre a primeira versão do catálogo RPGenda com dados dos providers existentes do Wger. Consulta e detalhes continuam funcionando sobre o modelo atual; criar exercício ainda utiliza o formulário existente do Wger.
- O catálogo também aceita `selecting: true` e `initialSelection`. A chamada retorna `List<Exercise>` ao confirmar e `null` ao cancelar, para que criação e edição de quest preservem seu próprio contexto. Esse modo ainda será conectado às novas telas de quest.
- Busca, filtros de categoria/equipamento existentes, estados de carregamento/erro e seleção funcionam sem inventar dados de exemplo. A seleção permanece ao buscar outro termo e não retorna IDs removidos do catálogo atual.
- “Criados/Padrão” ainda não foi implementado: a lista sincronizada atual não oferece um campo inequívoco de propriedade do exercício. Os créditos de autoria não serão tratados como propriedade do usuário.
- O visual do catálogo ainda está em adaptação: utiliza o filtro avançado existente, e as fontes GrapeSoda/Mokgech não estão disponíveis no repositório. Inter e Pixelify Sans foram incluídas com suas licenças; Pixelify é temporária nos títulos/botões que usam GrapeSoda no Figma.
- Esta entrega inicial não implementa as dez telas completas, as regras de XP/vidas, nem corrige a infraestrutura PowerSync. Essas partes continuam em aberto.
- Validação desta etapa: análise estática sem problemas; sete cenários funcionais de widget aprovados (seleção durante busca, alternância, cancelamento, detalhes/offline, falha de carregamento, remoção por sincronização e acessibilidade em tela pequena). A captura opcional de abertura e catálogo também foi executada e inspecionada. A execução em celular permanece pendente; o APK foi verificado conforme a seção abaixo.

### APK de teste pronto para instalação posterior

Em 26/10/2026, a compilação Android gerou o APK de debug. A instalação foi cancelada antes de qualquer atualização no celular. Ao retomar, o APK preservado passou na verificação ZIP e na verificação de assinatura APK v2; nenhum fonte havia sido alterado após sua geração, por isso não foi necessário recompilá-lo.

- Arquivo: `wger-flutter/build/app/outputs/flutter-apk/app-debug.apk`.
- Nome no Android: **RPGenda (teste)**; pacote: `de.wger.flutter.debug`; versão: `2.1.0` (270).
- Tamanho: aproximadamente **134 MiB**, ainda sem otimização de distribuição.
- O SHA-256 e os metadados estão ao lado do APK. A pasta `build/` é ignorada pelo Git.
- Situação em 27/10/2026: **instalado no Motorola Edge 60 Fusion**. O Android confirmou a instalação, a atividade abriu com `Status: ok` e o processo continuou ativo. A versão de teste anterior tinha assinatura diferente e foi removida, incluindo seus dados locais, com autorização explícita do usuário; a versão normal do Wger foi preservada. Login e sincronização reais continuam pendentes de teste.
- O APK está salvo em disco, sem depender das áreas temporárias em memória usadas durante a compilação.

### Diagnóstico no celular — 27/10/2026

- O APK instalado ainda é uma adaptação parcial: `MainApp._getHomeScreen` continua abrindo `HomeTabsScreen`, com Dashboard, rotinas, nutrição, medições e galeria do Wger. As dez telas do Figma não foram implementadas como navegação principal.
- O módulo `lib/features/rpgenda/` contém tema, botão e catálogo. A rota `/exercises` foi redirecionada ao catálogo; a abertura usa o logo. Login, seleção de servidor e Home ainda são os existentes do Wger.
- O código mantém servidores oficiais como padrões: `https://wger.de` e `https://dev.wger.de`. Portanto, o acesso ao serviço oficial ainda aparece no aplicativo. Uma variante RPGenda voltada ao backend próprio precisa definir explicitamente sua configuração de servidor.
- O log do processo no Android registrou timeout ao consultar `https://192.168.0.104/api/v2/version/`. O backend local verificado atende por `http://192.168.0.104`, porta 80; HTTPS local não foi configurado.
- O celular apresentou endereço `192.168.1.15/24`, em sub-rede diferente do PC (`192.168.0.104`). Uma tentativa HTTP do celular ao PC também expirou. Assim, corrigir apenas HTTPS para HTTP não comprova acesso pela rede atual.
- A API HTTP respondeu `2.8.0-dev.0` no PC. O PowerSync, diferente da verificação anterior, estava ativo e `/ps/probes/liveness` respondeu com `ready: true`. Nenhuma alteração desses serviços foi realizada durante este diagnóstico.
- Um encaminhamento USB foi criado para investigação e removido ao final, pois não foi obtida uma resposta HTTP por esse teste. Login e conectividade real do aplicativo continuam sem validação.

## Modelo para descrever cada tela

Copie e preencha em linguagem comum:

```text
Tela: [número/nome ou link Figma]
Objetivo:
Chego aqui quando:
Dados que aparecem e de onde vêm:
Ao tocar em [botão/elemento]: [efeito + próxima tela]
Para salvar/concluir, é obrigatório:
Ao voltar ou cancelar: [preservar rascunho / descartar / perguntar]
Sem conexão: [o que continua funcionando e o que fica pendente]
Se fechar e reabrir o app:
Quando não houver dados ou ocorrer erro:
Resultado esperado: [o que fica salvo e o que muda nas outras telas]
```

## Perguntas de produto que mudam a implementação

1. O protótipo passou a ter dez telas/estados. Confirmar o escopo da primeira implementação no aplicativo.
2. Como a Home escolhe a quest de hoje: agenda, rodízio ou escolha manual?
3. Quest equivale a um dia de treino dentro de uma rotina (confirmado). Pode ser repetida no mesmo dia?
4. O que representam mochila, pergaminho, halter e engrenagem na navegação inferior?
5. O que os indicadores `0/1` dos cards medem e quando reiniciam?
6. Criando exercício cria um item de catálogo reutilizável ou apenas configura um exercício na quest atual?
7. A criação de exercício abre pelo Adicionar do catálogo. Definir seleção múltipla e reordenação no aplicativo; o protótipo demonstra uma seleção.
8. O check conclui uma série? Pode desfazer? “Cabei” conclui o exercício e “Acabei” conclui a sessão? Pode finalizar com séries pendentes?
9. Há descanso automático? Qual duração? O que acontece ao pausar, sair e reabrir?
10. Como calcular XP, nível, vidas e recompensas? Por ora são decorativos ou precisam persistir e sincronizar?
11. Quais ações precisam funcionar offline: consultar, executar, criar/editar treino, criar exercício?
12. Primeiro acesso mantém login/configuração de servidor do Wger? Há um usuário local já cadastrado?

## Situação do ambiente

- Os dois projetos estão presentes; o diretório pai não é um repositório Git funcional.
- Em 26/10, foi validado Flutter 3.47.0 com Dart 3.13.0, compatível com o cliente. A instalação é acessada por `/home/samishii/flutter/bin/flutter`.
- Em 26/10, `flutter pub get` preparou as dependências do checkout sem alterar as versões travadas no lockfile.
- Docker está instalado. Na consulta autorizada, web, Nginx, PostgreSQL, Redis e worker estavam saudáveis; o PowerSync estava reiniciando. Os logs identificam falha de autenticação PostgreSQL do usuário `powersync_storage` (código 28P01), impedindo a inicialização do serviço de sincronização.
- O executável Dart localizado em `/home/samishii/flutter/bin/cache/dart-sdk/bin/dart` informa versão 3.13.0, compatível com a restrição declarada. Flutter e dependências ainda precisam ser validados para compilar o app.
- `wger-backend/docker/` aparece como conteúdo não rastreado no Git; preservar e revisar antes de modificar.
- O Compose presente publica Nginx na porta 80 e mantém a porta 8000 interna. Endereço e porta precisam acompanhar o Compose realmente escolhido.
- Ainda não foram validados compilação, login, conexão do celular ou sincronização real. Não foi iniciado nem alterado nenhum serviço.

## Ajustes ao relatório de estratégia

O relatório descreve uma arquitetura possível, mas não é uma especificação desta versão. Usar os endpoints e a sincronização encontrados no código; conferir o Compose em uso; configurar hosts explícitos para o ambiente local em vez de adotar `ALLOWED_HOSTS=['*']` como padrão; manter credenciais na infraestrutura de armazenamento seguro já existente. A extração Figma nesta sessão ocorre pela integração conectada, sem depender do script citado no relatório.

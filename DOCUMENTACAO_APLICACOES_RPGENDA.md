# RPGenda — inventário do que foi aplicado

Data do levantamento: **27/10/2026**.

Este documento reúne a implementação RPGenda presente no workspace, suas pastas, arquivos, recursos visuais, configurações e evidências de validação. O levantamento foi feito por leitura dos fontes, documentação e diferenças locais do Git. Testes, builds, servidor e instalação no celular não foram executados novamente para produzir este inventário.

## 1. Visão geral

O projeto utiliza o aplicativo Flutter do Wger como base e acrescenta a identidade visual e o fluxo de treinos do RPGenda. A entrada principal abre o novo módulo. O modo local está ativado por padrão, permitindo usar as funções principais sem login nem sincronização com servidor.

Foram aplicados:

- Fluxo de dez telas, incluindo criação e edição de quests e exercícios.
- Tema escuro, tipografia própria, logo, sprites, painéis e botões de aparência pixelada.
- Catálogo, seleção de exercícios e configuração de séries, repetições e carga por quest.
- Confirmação de quest, execução de treino, retomada de sessão e histórico.
- Persistência local com SharedPreferences e controle de estado com Riverpod.
- Adaptador para os modelos e serviços existentes do Wger, ainda pendente de validação real conectada.
- Ajustes na autenticação, rede e PowerSync para o modo local.
- Nome específico para o aplicativo Android de teste.
- Testes automatizados e dez capturas de revisão.

## 2. Organização das pastas

```text
RPGenda/
├── README.md                         # Apresentação da raiz
├── DOCUMENTACAO_APLICACOES_RPGENDA.md # Este inventário
├── FLUXO_OITO_TELAS.md               # Fluxo, decisões e histórico; hoje cobre dez telas
├── FOntes apagar dps/               # Fontes originais fornecidas pelo usuário
├── wger-flutter/                    # Aplicativo Flutter e implementação RPGenda
│   ├── lib/
│   │   ├── main.dart                # Entrada, tema e rotas
│   │   ├── core/                    # Infraestrutura e modo local
│   │   ├── database/               # Banco e sincronização existentes
│   │   ├── l10n/                   # Traduções
│   │   └── features/
│   │       ├── rpgenda/             # Novo módulo
│   │       │   ├── models.dart
│   │       │   ├── repository.dart
│   │       │   ├── controller.dart
│   │       │   ├── wger_gateway.dart
│   │       │   ├── theme.dart
│   │       │   ├── screens/
│   │       │   └── widgets/
│   │       └── ...                  # Funcionalidades originais do Wger
│   ├── assets/
│   │   ├── rpgenda/                 # Imagens e SVGs
│   │   └── fonts/rpgenda/           # Tipografia e arquivos de licença
│   ├── test/features/rpgenda/       # Testes do módulo
│   ├── docs/rpgenda/screenshots/    # Dez capturas de revisão
│   ├── android/                    # Configuração Android
│   ├── ios/                        # Estrutura iOS existente
│   ├── linux/                      # Estrutura Linux existente
│   ├── macos/                      # Estrutura macOS existente
│   ├── windows/                    # Estrutura Windows existente
│   ├── .github/scripts/            # Scripts existentes de automação
│   └── build/                      # Artefatos gerados localmente
└── wger-backend/                    # Backend Wger e infraestrutura Docker
    ├── wger/                       # Aplicação Django e seus módulos
    ├── settings/                   # Configurações
    ├── extras/                     # Ferramentas e recursos auxiliares
    └── docker/                     # Checkout de infraestrutura Docker
```

As plataformas e funcionalidades herdadas do Wger fazem parte da base do projeto; sua presença não comprova adaptação ou validação específica para o RPGenda. A pasta de fontes originais foi mantida, apesar do nome sugerir remoção futura.

## 3. Telas e navegação aplicadas

Os caminhos desta seção são relativos a `wger-flutter/`.

| Tela ou destino | Arquivo | Comportamento aplicado |
| --- | --- | --- |
| Abertura | `lib/core/splash_screen.dart` e `lib/features/rpgenda/screens/app.dart` | Logo e abertura do fluxo RPGenda |
| Home | `lib/features/rpgenda/screens/app.dart` | Quest selecionada, estado inicial sem quest e retomada do treino ativo |
| Minhas quests | `lib/features/rpgenda/screens/app.dart` | Lista de quests e acesso à criação, edição e seleção |
| Confirmar quest | `lib/features/rpgenda/screens/app.dart` | Confirmação e troca da quest antes do treino |
| Treino ativo | `lib/features/rpgenda/screens/workout.dart` | Séries, repetições, carga, marcação e conclusão |
| Criar quest | `lib/features/rpgenda/screens/editors.dart` | Composição de quest com exercícios e prescrições |
| Criar exercício | `lib/features/rpgenda/screens/editors.dart` | Cadastro de exercício personalizado |
| Catálogo do fluxo | `lib/features/rpgenda/screens/editors.dart` | Busca, filtros e seleção múltipla |
| Editar quest | `lib/features/rpgenda/screens/editors.dart` | Alteração dos dados e exercícios da quest |
| Editar exercício | `lib/features/rpgenda/screens/editors.dart` | Alteração de exercício personalizado |
| Histórico / mochila | `lib/features/rpgenda/screens/app.dart` | Consulta das sessões concluídas |
| Configurações / engrenagem | `lib/features/rpgenda/screens/app.dart` | Destino auxiliar e acesso ao catálogo |
| Catálogo integrado ao Wger | `lib/features/rpgenda/screens/exercise_catalogue_screen.dart` | Rota de exercícios existente, consulta e seleção usando providers do Wger |

Existem dois componentes de catálogo: `RpgCatalogue`, usado nos editores do fluxo principal, e `ExerciseCatalogueScreen`, usado na rota integrada ao Wger. Eles têm fontes de dados e contextos distintos.

Histórico e configurações são destinos auxiliares provisórios; não correspondem a duas telas adicionais do escopo visual original.

## 4. Regras funcionais implementadas

### Quests e exercícios

- Uma quest representa um dia de treino (`Day`) de uma rotina (`Routine`) Wger.
- Séries, repetições e carga pertencem à prescrição do exercício naquela quest, sem se tornarem atributos globais do catálogo.
- Criação e edição de quests com seleção de exercícios, remoção e reordenação.
- Reordenação por pressionamento prolongado no editor.
- Configuração da prescrição ao tocar no exercício da quest.
- Busca e filtros Todos/Criados/Padrão no catálogo do fluxo principal.
- Catálogo inicial local com oito exercícios.
- Criação e edição de exercícios personalizados.
- Confirmação antes de descartar alterações de formulário.
- Rascunhos preservados durante a navegação; não restaurados após encerramento do processo.

### Execução e histórico

- Início do treino após confirmação da quest.
- Alteração de repetições e carga nas séries do treino ativo.
- Marcação e desmarcação de séries, além de inclusão de novas séries.
- Finalização com pelo menos uma série concluída.
- Confirmação adicional ao finalizar com séries pendentes.
- Persistência da sessão ativa para retomada pela Home.
- Registro da sessão concluída no histórico.
- Contador da Home baseado nas sessões concluídas no dia, com meta visual de uma sessão.

### Seleção da Home

A sessão ativa tem prioridade. Sem sessão ativa, a Home utiliza a quest escolhida ou a primeira disponível. Essa escolha ainda não representa uma agenda automática.

## 5. Arquitetura do módulo

| Arquivo em `lib/features/rpgenda/` | Responsabilidade |
| --- | --- |
| `models.dart` | Modelos `RpgExercise`, `RpgPrescription`, `RpgQuest`, `RpgSet`, `RpgWorkout` e `RpgendaData`; representação dos dados persistidos |
| `repository.dart` | Interface de armazenamento, implementação SharedPreferences e leitura/gravação do estado em JSON |
| `controller.dart` | Carregamento e mudanças de estado: selecionar/salvar quest, salvar exercício, iniciar treino, editar/adicionar séries e finalizar |
| `wger_gateway.dart` | Adaptação aos serviços e modelos do Wger para catálogo, quests, exercícios, sessões e logs |
| `theme.dart` | Cores, tipografia e aplicação do tema RPGenda |
| `screens/app.dart` | Abertura, navegação, Home, quests, confirmação, histórico e configurações |
| `screens/editors.dart` | Catálogo do fluxo, editor de quest e editor de exercício |
| `screens/workout.dart` | Execução e edição das séries do treino |
| `screens/exercise_catalogue_screen.dart` | Catálogo conectado aos providers existentes do Wger |
| `widgets/pixel_button.dart` | Botão reutilizável com tons primário, ação e perigo |
| `widgets/design.dart` | Componentes `SpriteCrop`, `RpgHeading`, `RpgPanel`, `RpgInputFrame`, `RpgPage`, `Paper` e `RpgPlayerHeader` |

O controller serializa mudanças e publica o novo estado após a gravação. Os testes incluem falha de persistência e recuperação de gravações posteriores.

### Armazenamento

- Modo local: chave `rpgenda.local.v1`.
- Modo conectado: chave `rpgenda.server.<identidade codificada>.v1`, separando os registros por identidade de servidor/conta.
- Dados: quests, exercícios personalizados, seleção atual, sessão ativa e histórico.
- Os registros do modo local não são enviados automaticamente ao backend.
- Drift/SQLite e PowerSync permanecem na arquitetura herdada. Não foi acrescentado Hive para este módulo.

## 6. Alterações na base Flutter existente

| Arquivo | Alteração identificada no Git |
| --- | --- |
| `lib/main.dart` | Título RPGenda, tema próprio, entrada e rota principal para `RpgendaApp`, rota de exercícios para o catálogo adaptado e suspensão do roteador de links no modo local |
| `lib/core/local_mode.dart` | Nova constante `WGER_LOCAL_MODE`, com padrão `true` |
| `lib/core/splash_screen.dart` | Fundo RPGenda e logo local |
| `lib/core/home_tabs_screen.dart` | Suspensão da sincronização automática de saúde/fuso na abertura em modo local |
| `lib/core/network/auth_notifier.dart` | Entrada sem login, sem renovação de token e sem limpeza de sessão pelos caminhos protegidos no modo local |
| `lib/core/network/network_provider.dart` | Estado de rede offline e suspensão das sondagens no modo local |
| `lib/database/powersync/powersync.dart` | Banco local disponível sem conexão ao serviço PowerSync no modo local |
| `lib/l10n/app_en.arb` | Textos do catálogo em inglês |
| `lib/l10n/app_pt_BR.arb` | Textos do catálogo em português brasileiro |
| `pubspec.yaml` | Registro dos assets e famílias tipográficas RPGenda |
| `android/app/build.gradle` | Nome debug alterado para `RPGenda (teste)`; sufixo `.debug` já existente preservado |
| `README.md` | Instruções de modo local, fluxo, limitações, validação e instalação registrada |

A tradução adicionada ao catálogo não significa que todas as novas telas tenham localização completa em todos os idiomas do Wger.

## 7. Identidade visual, assets e fontes

### Tema

| Token | Cor |
| --- | --- |
| Fundo | `#171717` |
| Superfície | `#28251C` |
| Borda | `#453F30` |
| Laranja | `#F54400` |
| Azul | `#3A63E1` |
| Texto | `#E2E8F0` |

O tema força aparência escura nas variantes às quais é aplicado. Inter é a família base; títulos usam GrapeSoda, e a composição visual também dispõe das demais fontes registradas.

### Fontes em `assets/fonts/rpgenda/`

- `GrapeSoda.ttf`.
- `Mokgech-Regular.ttf`.
- `Mokgech-Italic.ttf`.
- `PixelifySans.ttf`.
- `Inter.ttf`.
- `OFL.txt` e `Inter-OFL.txt`: arquivos de licença presentes nessa pasta.

GrapeSoda e Mokgech foram copiadas das fontes fornecidas pelo usuário em `FOntes apagar dps/`. A presença dos arquivos OFL não comprova que cubram todas as famílias da pasta. RobotoCondensed e seu arquivo `assets/fonts/OFL - RobotoCondensed.txt` também estão na base; não aparecem como modificação local neste levantamento.

### Imagens em `assets/rpgenda/`

Logo, sprites, elementos de composição, bordas e ícones exportados para a interface. Inventário dos arquivos presentes:

- `023e5.png`.
- `0ba10.svg`.
- `0dd76.svg`.
- `1b253.svg`.
- `2b99d.svg`.
- `2f09e.svg`.
- `3346a.svg`.
- `34cbd.svg`.
- `39cd9.svg`.
- `5655b.svg`.
- `5e0d3.svg`.
- `77a2f.svg`.
- `7920b.svg`.
- `82e17.png`.
- `8dde8.png`.
- `8ed92.png`.
- `a2d07.png`.
- `a3b6c.svg`.
- `aeab8.svg`.
- `back.svg`.
- `c523c.png`.
- `c64a4.svg`.
- `d414c.png`.
- `ee27b.svg`.
- `f6805.png`.
- `f7467.png`.
- `faf57.svg`.
- `fecea.png`.
- `input-border.svg`.
- `logo.png`.

## 8. Backend e infraestrutura existentes

`wger-backend/` contém a aplicação Django Wger. Entre os módulos existentes estão `core`, `manager`, `exercises`, `nutrition`, `weight`, `measurements`, `gallery`, `gym`, `trophies`, `mailer`, `software`, `config` e `utils`, além das traduções em `locale`.

O adaptador Flutter reutiliza rotinas, dias, slots, prescrições, sessões e logs. Os endpoints registrados na documentação técnica incluem `/api/v2/routine/`, `/api/v2/workoutsession/`, `/api/v2/workoutlog/`, `/api/v2/exercise/`, `/api/v2/exerciseinfo/` e `/api/v2/exercise-submission/`.

No levantamento do Git, não foram encontradas alterações rastreadas no backend; `docker/` aparece como diretório não rastreado e contém um checkout próprio. Há arquivos Compose, exemplos de configuração, ambientes de desenvolvimento e PostgreSQL, testes e Grafana. Sua presença não comprova que os serviços estejam em execução ou que tenham sido configurados nesta etapa.

O sistema de troféus já existente no Wger não define as regras de XP, vidas ou recompensas do RPGenda.

## 9. Scripts e automação

Os arquivos abertos no IDE foram considerados no levantamento:

| Arquivo | Finalidade | Situação no Git consultado |
| --- | --- | --- |
| `.github/scripts/take-screenshots.sh` | Capturas de integração em cinco blocos de idiomas, com tentativas, limite de tempo e recuperação de dispositivo Android | Sem modificação local identificada |
| `.github/scripts/portrait-avd.sh` | Ajuste das dimensões do emulador para orientação retrato | Sem modificação local identificada |

Esses scripts fazem parte da infraestrutura existente. As capturas das dez telas RPGenda foram documentadas como geradas pelo teste do módulo, e não como fotografias do celular.

## 10. Testes e capturas

### Arquivos de teste

`test/features/rpgenda/flow_test.dart` cobre persistência após reabertura, prescrições por quest, sessão ativa, histórico, falha de gravação, dados inválidos e percurso das dez telas com criação, edição e conclusão.

`test/features/rpgenda/exercise_catalogue_screen_test.dart` cobre seleção durante busca, seleção inicial sem duplicação, cancelamento, consulta, bloqueio de criação offline no catálogo integrado, erro de carregamento, exercício removido por sincronização, tela pequena com texto ampliado e geração opcional de capturas.

A documentação existente registra **12 testes do módulo aprovados**, análise e compilação de bundle/APK. Esse resultado é histórico e não foi repetido neste levantamento.

### Capturas em `docs/rpgenda/screenshots/`

| Arquivo | Tela |
| --- | --- |
| `01-splash.png` | Abertura |
| `02-home-empty.png` | Home no primeiro acesso |
| `03-quests.png` | Minhas quests |
| `04-confirmation.png` | Confirmar quest |
| `05-active.png` | Treino ativo |
| `06-create-quest.png` | Criar quest |
| `07-create-exercise.png` | Criar exercício |
| `08-catalogue.png` | Catálogo |
| `09-edit-quest.png` | Editar quest |
| `10-edit-exercise.png` | Editar exercício |

As capturas são documentadas em 402 × 900 px, com dados de teste sem acesso à rede. Também existe verificação em 320 × 568 px.

## 11. Execução e verificação

Executar a partir de `wger-flutter/`:

```bash
# Modo local padrão
flutter run

# Autenticação e sincronização originais habilitadas
flutter run --dart-define=WGER_LOCAL_MODE=false

# Análise do módulo
flutter analyze lib/features/rpgenda lib/main.dart test/features/rpgenda/flow_test.dart

# Testes RPGenda
flutter test test/features/rpgenda

# Capturas do fluxo em /tmp
flutter test test/features/rpgenda/flow_test.dart --dart-define=RPGenda_SCREENSHOTS=/tmp

# Bundle debug
flutter build bundle --debug
```

Os testes existentes de autenticação e sincronização devem usar `--dart-define=WGER_LOCAL_MODE=false`, conforme o README.

### APK registrado

- Arquivo presente: `wger-flutter/build/app/outputs/flutter-apk/app-debug.apk`.
- Arquivos auxiliares presentes: `.apk.json`, `.apk.sha1` e `.apk.sha256`.
- Registro histórico: compilação em 27/10/2026 às 11:04, horário de São Paulo; ARM64, debug, modo local.
- Pacote registrado: `de.wger.flutter.debug`.
- Versão registrada: `2.1.0 (270)`.
- Tamanho registrado: 130,76 MiB.
- O histórico relata verificação de integridade e assinatura APK v2.
- `FLUXO_OITO_TELAS.md` e o README Flutter registram instalação e abertura no Motorola Edge 60 Fusion em 27/10/2026, preservando o pacote normal `de.wger.flutter`.

Existe divergência documental: `docs/rpgenda/README.md` ainda diz que a instalação dessa versão não foi verificada, enquanto os registros da raiz e do README Flutter relatam a instalação posterior. Este inventário registra a divergência sem afirmar uma nova verificação do dispositivo ou do APK.

## 12. Pendências e limites atuais

- Validar o adaptador conectado contra backend e PowerSync reais.
- Confirmar permissões de conta para criação de exercícios no servidor.
- Definir agenda, frequência e repetição das quests.
- Definir descanso automático.
- Definir XP, nível, vidas, insígnias e recompensas; o cabeçalho atual é provisório.
- Definir telas próprias para histórico/mochila e configurações, se exigidas pelo produto.
- Restaurar rascunhos após encerramento do processo, caso desejado.
- Definir migração dos dados locais para servidor, se necessária; ela não ocorre automaticamente.
- Conciliar os registros históricos de build/instalação na documentação.

## 13. Estado das alterações e referências

No início deste levantamento, o Git Flutter apresentava 11 arquivos rastreados modificados e seis entradas novas: `assets/fonts/rpgenda/`, `assets/rpgenda/`, `docs/`, `lib/core/local_mode.dart`, `lib/features/rpgenda/` e `test/features/rpgenda/`. Isso inclui arquivos ainda não commitados. Não foi inferida autoria de alterações anteriores.

Este trabalho acrescentou somente este documento; não alterou o aplicativo, o backend ou os arquivos de documentação anteriores.

Referências locais:

- [Fluxo, decisões e histórico](FLUXO_OITO_TELAS.md).
- [README do aplicativo](wger-flutter/README.md).
- [Índice das capturas](wger-flutter/docs/rpgenda/README.md).
- [Código do módulo RPGenda](wger-flutter/lib/features/rpgenda/).
- [Testes do módulo](wger-flutter/test/features/rpgenda/).

# RPGenda — fluxo de telas e protótipo

Protótipo revisado em 26/10/2026. Referência: página `Telas front-end` (`307:5581`) do arquivo Figma `weylcyY14ApShovzlavwJc`. As verificações do ambiente descritas adiante são de 21/10/2026 e não foram repetidas nesta edição.

O fluxo abaixo descreve o protótipo atual. Regras de persistência, agenda, XP e sincronização continuam pendentes. Por orientação do usuário, cards e controles repetidos são exemplos do mesmo comportamento: não é necessário conectar cada repetição para definir o fluxo do produto.

## Telas encontradas

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

Decisão pendente: uma quest representa uma rotina Wger inteira ou um dia de treino dentro de uma rotina? A escolha afeta criação, agenda, execução e histórico. Séries, repetições e carga são prescrições da participação do exercício no treino; não devem virar propriedades globais do exercício de catálogo.

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
3. Quest equivale a rotina ou dia? Pode ser repetida no mesmo dia?
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
- O cliente declara Dart `>=3.10.0 <4.0.0`. Flutter/Dart não estão no PATH da sessão, mas foram localizadas instalações no diretório do usuário; compatibilidade ainda não foi executada.
- Não existe `wger-flutter/.dart_tool/package_config.json`: dependências do cliente não estão preparadas nesse checkout.
- Docker está instalado. Na consulta autorizada, web, Nginx, PostgreSQL, Redis e worker estavam saudáveis; o PowerSync estava reiniciando. Os logs identificam falha de autenticação PostgreSQL do usuário `powersync_storage` (código 28P01), impedindo a inicialização do serviço de sincronização.
- O executável Dart localizado em `/home/samishii/flutter/bin/cache/dart-sdk/bin/dart` informa versão 3.13.0, compatível com a restrição declarada. Flutter e dependências ainda precisam ser validados para compilar o app.
- `wger-backend/docker/` aparece como conteúdo não rastreado no Git; preservar e revisar antes de modificar.
- O Compose presente publica Nginx na porta 80 e mantém a porta 8000 interna. Endereço e porta precisam acompanhar o Compose realmente escolhido.
- Ainda não foram validados compilação, login, conexão do celular ou sincronização real. Não foi iniciado nem alterado nenhum serviço.

## Ajustes ao relatório de estratégia

O relatório descreve uma arquitetura possível, mas não é uma especificação desta versão. Usar os endpoints e a sincronização encontrados no código; conferir o Compose em uso; configurar hosts explícitos para o ambiente local em vez de adotar `ALLOWED_HOSTS=['*']` como padrão; manter credenciais na infraestrutura de armazenamento seguro já existente. A extração Figma nesta sessão ocorre pela integração conectada, sem depender do script citado no relatório.

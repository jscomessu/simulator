# Simulador de Linha de Montagem Industrial 2D

**Português** | [日本語](#産業用組立ライン2dシミュレーター)

## Introdução

Este simulador foi desenvolvido para representar, de forma visual e quantitativa, a operação de uma linha de montagem industrial composta por cinco estações principais (`M1` a `M5`) e cinco grupos de submontagem (`S1` a `S5`), cada um com três operações sequenciais.

A ferramenta permite testar diferentes configurações produtivas antes de alterar uma linha real. Seu foco é apoiar empresas de trabalho temporário e terceirização — **haken kaisha** (派遣会社) e **ukeoi kaisha** (請負会社) — no dimensionamento da mão de obra, na estimativa do impacto financeiro e na avaliação do prazo necessário para concluir um lote.

O simulador roda inteiramente no navegador. Não exige instalação, servidor, banco de dados ou envio de informações para serviços externos.

## Finalidade

O objetivo principal não é fornecer uma única resposta definitiva, mas permitir a comparação de cenários, por exemplo:

- uma, duas ou três linhas principais paralelas;
- diferentes quantidades de linhas paralelas de submontagem;
- um ou dois turnos;
- diferentes durações de hora extra por turno;
- trabalho de sábado (*shukkin*) configurado separadamente para cada turno;
- diferentes capacidades dos buffers;
- diferentes tempos de montagem;
- diferentes quantidades de produtos finais;
- diferentes custos horários e percentuais de adicionais;
- impacto da configuração sobre prazo, utilização, bloqueio, espera por material e custo unitário.

A ferramenta pode ajudar na elaboração de propostas comerciais, no planejamento de efetivos, na análise preliminar de capacidade e na discussão de melhorias entre a fábrica contratante e a empresa fornecedora de mão de obra.

## Classificação do modelo

Este é um **simulador de eventos discretos híbrido**, com entradas determinísticas e tempos operacionais estocásticos.

São determinísticos os dados definidos diretamente pelo usuário, como:

- quantidade do lote;
- data de início;
- número de turnos;
- jornada e horas extras;
- horas de *shukkin*;
- quantidade de linhas;
- capacidade dos buffers;
- custo horário e percentuais de adicionais.

Os tempos de montagem são estocásticos. Cada operação recebe um tempo sorteado a partir de uma **distribuição triangular**, definida por três valores:

- mínimo;
- mais provável;
- máximo.

Portanto, o modelo não é totalmente determinístico. Ele utiliza números pseudoaleatórios e amostragem do tipo Monte Carlo durante cada execução. Entretanto, é mais delimitado do que um modelo industrial abrangente construído em plataformas como Arena, Simio ou AnyLogic: não inclui automaticamente todas as fontes possíveis de incerteza, calendários complexos, falhas, manutenção, absenteísmo ou logística externa.

> O Arena não é, por definição, “mais estocástico”. Ele é uma plataforma geral de simulação que pode representar tanto modelos determinísticos quanto estocásticos. A diferença está na abrangência e no nível de detalhamento do modelo construído.

## Distribuição triangular

A distribuição triangular é adequada quando existem dados limitados, mas o analista consegue estimar o menor tempo possível, o tempo mais provável e o maior tempo razoável para uma operação.

Ela é útil para estudos preliminares, mas não substitui a análise de tempos reais. Quando houver histórico suficiente, recomenda-se verificar se outra distribuição representa melhor cada processo.

## Semente aleatória

A semente controla a sequência de números pseudoaleatórios. O mesmo conjunto de parâmetros com a mesma semente deve produzir a mesma sequência de tempos sorteados, facilitando auditoria e comparação.

Para avaliar a variabilidade de um cenário:

1. mantenha todos os parâmetros iguais;
2. execute o modelo com várias sementes;
3. registre prazo, produção, utilização e custos;
4. compare média, amplitude, desvio e percentis;
5. só depois compare o cenário com outra configuração de linhas ou mão de obra.

Uma única execução é uma realização possível do sistema, não uma previsão exata.

## Estrutura produtiva representada

### Linha principal

A linha principal possui cinco etapas sequenciais:

`M1 → M2 → M3 → M4 → M5 → produto concluído`

Podem ser ativadas até três linhas principais paralelas. Cada posto ativo utiliza uma pessoa por turno.

### Submontagens

Cada estação principal recebe um subconjunto produzido por seu grupo correspondente:

- `S1.1 → S1.2 → S1.3 → buffer S1 → M1`;
- `S2.1 → S2.2 → S2.3 → buffer S2 → M2`;
- `S3.1 → S3.2 → S3.3 → buffer S3 → M3`;
- `S4.1 → S4.2 → S4.3 → buffer S4 → M4`;
- `S5.1 → S5.2 → S5.3 → buffer S5 → M5`.

Cada grupo de submontagem pode utilizar de zero a três linhas paralelas completas. Cada posto ativo acrescenta uma pessoa por turno.

## Calendário de trabalho

- **Turno 1:** 08:00–17:00, com uma hora de intervalo sem produção.
- **Turno 2:** 19:00–04:00, com uma hora de intervalo sem produção.
- A produção começa às 08:00 da data selecionada.
- A data inicial deve estar entre segunda e sexta-feira.
- Não há produção nos intervalos.
- Não há produção aos domingos.
- O trabalho de sábado (*shukkin*) é configurado separadamente para cada turno.
- As horas extras são configuradas por turno dentro dos limites oferecidos pela interface.

O calendário representa uma hipótese operacional do estudo. Antes de utilizar os resultados em uma proposta ou escala real, a empresa deve verificar contratos, acordos internos e legislação aplicável.

## Custos de mão de obra

O modelo recebe uma tarifa horária bruta por pessoa e calcula:

- custo das horas normais;
- custo das horas extras;
- custo de *shukkin*;
- adicional noturno;
- custo direto acumulado;
- custo por produto concluído;
- custo médio por funcionário no período simulado;
- custo separado por turno.

Os resultados representam custos diretos calculados conforme os parâmetros informados. Impostos, encargos sociais, despesas administrativas, recrutamento, transporte, uniformes, treinamento, margem comercial e *shōhizei* devem ser acrescentados separadamente quando aplicáveis.

## Indicadores operacionais

O relatório apresenta indicadores gerais e por estação:

- produtos concluídos;
- produção em processo;
- subconjuntos prontos;
- data e hora de término do lote;
- dias, horas e minutos decorridos;
- pessoas por turno e quadro total;
- produção acumulada;
- custo acumulado;
- utilização;
- tempo aguardando material;
- bloqueio e tempo bloqueado;
- composição do custo;
- produção por linha principal.

**Aguardando material** significa que a estação está disponível, mas não possui todos os componentes necessários para iniciar a operação.

**Bloqueada** significa que a estação concluiu uma operação, mas não consegue liberar a peça porque o buffer ou a etapa seguinte não possui capacidade disponível.

## Dashboard e relatórios

Ao final da simulação, o dashboard apresenta:

- título e identificação da simulação;
- data e hora de início e término;
- configuração de linhas utilizada;
- desenho superior da linha de produção;
- parâmetros de entrada;
- resultados gerais;
- gráficos com escalas nos eixos;
- indicadores por estação com ordenação por clique;
- quadro consolidado de custo médio por funcionário.

As saídas podem ser abertas em uma janela separada e exportadas como:

- HTML;
- CSV;
- PDF, por meio da função de impressão do navegador.

O dashboard possui interface em português e japonês.

## Como utilizar

1. Abra o arquivo `index.html` em um navegador atualizado.
2. Informe a quantidade de produtos finais, limitada a 10.000 unidades.
3. Selecione a data inicial.
4. Defina um ou dois turnos.
5. Configure hora extra e *shukkin* por turno.
6. Informe custos, adicionais e capacidades dos buffers.
7. Ajuste os tempos triangulares de cada estação e subestação.
8. Escolha a quantidade de linhas principais e de submontagem.
9. Aplique a configuração.
10. Ajuste a velocidade e inicie a simulação.
11. Ao concluir, abra o dashboard e salve os resultados.

## Comparação correta de cenários

Para comparar configurações, altere um conjunto controlado de parâmetros por vez. Uma tabela de estudo pode conter:

| Cenário | Linhas M | Linhas S | Turnos | HE | Shukkin | Pessoas/turno | Prazo | Custo total | Custo unitário |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| Base | 1 | 1/1/1/1/1 | 2 | 0 h | 0 h | — | — | — | — |
| A | 2 | 1/1/1/1/1 | 2 | 0 h | 0 h | — | — | — | — |
| B | 2 | 2/2/2/2/2 | 2 | 1 h | 8 h | — | — | — | — |

Use várias sementes para cada cenário. A melhor configuração não é necessariamente aquela com o menor prazo: deve-se avaliar conjuntamente custo, risco de atraso, utilização, filas, bloqueios e ociosidade.

## Limitações

Esta versão não representa automaticamente:

- faltas e substituição de trabalhadores;
- curva de aprendizagem;
- diferenças individuais de produtividade;
- fadiga e perda de rendimento;
- falhas de máquinas e manutenção;
- refugo, retrabalho e inspeções adicionais;
- indisponibilidade de matéria-prima externa;
- tempos de setup e troca de modelo, salvo se incorporados aos tempos informados;
- restrições ergonômicas e de segurança;
- custos indiretos completos;
- otimização matemática automática;
- múltiplas replicações automáticas com intervalo de confiança.

Os resultados devem ser tratados como apoio à decisão e comparação de cenários, não como garantia contratual de produtividade, custo ou prazo.

## Publicação no GitHub Pages

O arquivo principal deve se chamar `index.html` e ficar na raiz do repositório. No GitHub:

1. abra **Settings → Pages**;
2. selecione **Deploy from a branch**;
3. escolha a branch `main` e a pasta `/(root)`;
4. salve e aguarde a publicação.

O endereço publicado poderá ser aberto diretamente ou incorporado ao Google Sites por URL.

## Arquivos

| Arquivo | Finalidade |
|---|---|
| `index.html` | Simulador completo e executável |
| `README.md` | Apresentação, método e instruções |

## Uso responsável

Antes da aplicação em uma operação real, valide os tempos, capacidades, salários, adicionais, escalas e premissas com responsáveis pela produção, engenharia industrial, recursos humanos, segurança do trabalho e conformidade legal.

---

# 産業用組立ライン2Dシミュレーター

**[Português](#simulador-de-linha-de-montagem-industrial-2d)** | **日本語**

## はじめに

本シミュレーターは、5つの主工程（`M1`～`M5`）と、各3工程から成る5つのサブ組立グループ（`S1`～`S5`）を備えた産業用組立ラインを、視覚的かつ定量的に表現するために開発されました。

実際の生産ラインを変更する前に、複数の生産条件を比較できます。主な対象は、**派遣会社**および**請負会社**であり、必要人員の算定、労務費への影響、ロット完了までの期間を検討するための支援ツールです。

本シミュレーターはブラウザー内で動作し、インストール、サーバー、データベース、外部サービスへのデータ送信を必要としません。

## 目的

本ツールの目的は、唯一の確定的な答えを出すことではなく、次のような複数のシナリオを比較することです。

- 主ライン1～3本の比較
- サブ組立ライン数の比較
- 1直または2直勤務
- 各直の残業時間
- 各直ごとの土曜出勤
- バッファ容量
- 各工程の作業時間
- 完成品の注文数量
- 時間単価および各種割増率
- 納期、稼働率、ブロック、材料待ち、製品単価への影響

派遣先・発注企業と人材供給会社との間で、見積り、人員計画、生産能力および改善案を検討する際に利用できます。

## モデルの分類

本モデルは、**確定的な入力条件と確率的な作業時間を組み合わせたハイブリッド型離散事象シミュレーション**です。

ユーザーが直接設定する次の条件は確定的です。

- 注文数量
- 生産開始日
- シフト数
- 所定労働時間および残業時間
- 土曜出勤時間
- ライン数
- バッファ容量
- 時間単価および割増率

一方、各組立作業の所要時間は確率的です。作業開始時に、次の3値で定義された**三角分布**から所要時間を抽出します。

- 最小値
- 最頻値
- 最大値

したがって、本モデルは完全な確定モデルではありません。各実行では疑似乱数とモンテカルロ型サンプリングを使用します。ただし、Arena、Simio、AnyLogicなどで構築される包括的な産業モデルと比べると、対象範囲を限定しています。設備故障、保全、欠勤、複雑な勤務カレンダー、外部物流など、すべての不確実性を自動的に含むものではありません。

> Arena自体が「より確率的」であるわけではありません。Arenaは、確定モデルと確率モデルの両方を構築できる汎用シミュレーション環境です。違いは、構築するモデルの範囲と詳細度にあります。

## 三角分布

三角分布は、十分な実績データがない場合でも、最短時間、最も起こりやすい時間、合理的な最長時間を見積もることができる工程に適しています。

初期検討には有効ですが、実測データの分析に代わるものではありません。十分な履歴データが得られた場合は、各工程により適した分布がないか確認することを推奨します。

## 乱数シード

乱数シードは疑似乱数の生成順序を制御します。同一の設定と同一のシードを使用すれば、同じ作業時間系列を再現でき、監査や比較が容易になります。

シナリオのばらつきを評価する場合は、次の手順を推奨します。

1. シード以外の条件を固定する。
2. 複数のシードで実行する。
3. 納期、生産数、稼働率およびコストを記録する。
4. 平均値、範囲、標準偏差およびパーセンタイルを比較する。
5. その後に、ライン数や人員構成の異なるシナリオと比較する。

1回の実行結果は、起こり得る結果の一例であり、正確な予測値ではありません。

## 生産ライン構成

### 主ライン

主ラインは次の5工程で構成されます。

`M1 → M2 → M3 → M4 → M5 → 完成品`

主ラインは最大3本まで並列化できます。稼働する各工程には、各直1名を配置する前提です。

### サブ組立

各主工程には対応するサブ組立品が供給されます。

- `S1.1 → S1.2 → S1.3 → S1バッファ → M1`
- `S2.1 → S2.2 → S2.3 → S2バッファ → M2`
- `S3.1 → S3.2 → S3.3 → S3バッファ → M3`
- `S4.1 → S4.2 → S4.3 → S4バッファ → M4`
- `S5.1 → S5.2 → S5.3 → S5バッファ → M5`

各サブ組立グループには、0～3本の並列ラインを設定できます。稼働する各工程には、各直1名が追加されます。

## 勤務カレンダー

- **1直目：** 08:00～17:00、休憩1時間（生産停止）
- **2直目：** 19:00～翌04:00、休憩1時間（生産停止）
- 選択日の08:00から生産を開始します。
- 開始日は月曜日から金曜日までです。
- 休憩時間中は生産しません。
- 日曜日は生産しません。
- 土曜出勤は直ごとに設定します。
- 残業時間は画面上の範囲内で直ごとに設定します。

この勤務カレンダーはシミュレーション上の前提です。実際の見積りや勤務計画に使用する前に、契約、社内協定および適用法令を確認してください。

## 労務費

1人当たりの基本時間単価を入力し、次の費用を計算します。

- 通常時間コスト
- 残業コスト
- 土曜出勤コスト
- 深夜割増
- 累積直接労務費
- 完成品1個当たりのコスト
- シミュレーション期間における作業者1人当たり平均コスト
- 直別コスト

表示される金額は、入力条件に基づく直接費の試算です。税金、社会保険、管理費、採用費、交通費、制服、教育費、営業利益および消費税などは、必要に応じて別途加算してください。

## 運用指標

本レポートでは、全体および工程別に次の指標を表示します。

- 完成品数
- 仕掛品数
- 完成済みサブ組立品数
- ロット完了日時
- 開始からの経過日数・時間・分
- 各直人数および総人員
- 累積生産数
- 累積コスト
- 稼働率
- 材料待ち時間
- ブロック率およびブロック時間
- コスト構成
- 主ライン別生産数

**材料待ち**とは、工程が作業可能であるものの、開始に必要な部品が揃っていない状態です。

**ブロック**とは、作業が完了しているものの、後工程またはバッファに空きがなく、製品を送り出せない状態です。

## ダッシュボードと出力

シミュレーション終了後のダッシュボードには、次の内容が表示されます。

- レポート名およびシミュレーション識別情報
- 開始・終了日時
- 使用したライン構成
- 生産ラインの平面図
- 入力条件
- 総合結果
- 軸目盛付きグラフ
- クリックで並べ替え可能な工程別指標
- 作業者1人当たり平均コストの集計欄

出力は別ウィンドウで開き、次の形式で保存できます。

- HTML
- CSV
- ブラウザーの印刷機能によるPDF

ダッシュボードはポルトガル語と日本語に対応しています。

## 使用方法

1. 最新ブラウザーで `index.html` を開く。
2. 完成品の注文数量を入力する（最大10,000個）。
3. 生産開始日を選択する。
4. 1直または2直を選択する。
5. 各直の残業および土曜出勤を設定する。
6. 労務費、割増率およびバッファ容量を設定する。
7. 各主工程・サブ工程の三角分布時間を設定する。
8. 主ライン数とサブ組立ライン数を選択する。
9. 設定を適用する。
10. シミュレーション速度を調整して開始する。
11. 完了後、ダッシュボードを開いて結果を保存する。

## シナリオ比較の方法

条件を比較する際は、管理するパラメーターだけを変更し、それ以外を固定してください。各シナリオを複数のシードで実行することを推奨します。

最短納期のシナリオが必ずしも最適とは限りません。コスト、納期遅延リスク、稼働率、仕掛り、ブロックおよび待ち時間を総合的に評価してください。

## 制限事項

現バージョンでは、次の項目を自動的にはモデル化していません。

- 欠勤および代替要員
- 習熟曲線
- 作業者ごとの生産性差
- 疲労による能率低下
- 設備故障および保全
- 不良、手直しおよび追加検査
- 外部からの材料供給停止
- 段取り替え（入力時間に含めた場合を除く）
- 人間工学・安全上の制約
- 完全な間接費
- 数理最適化
- 信頼区間を伴う自動反復実行

結果は、意思決定とシナリオ比較の支援情報として使用してください。生産性、費用または納期を契約上保証するものではありません。

## GitHub Pagesでの公開

実行ファイルの名前を `index.html` とし、リポジトリのルートに配置します。

1. **Settings → Pages**を開く。
2. **Deploy from a branch**を選択する。
3. `main`ブランチと`/(root)`を選択する。
4. 保存して公開を待つ。

公開URLは、直接ブラウザーで開くことも、Google SitesにURLとして埋め込むこともできます。

## ファイル

| ファイル | 用途 |
|---|---|
| `index.html` | シミュレーター本体 |
| `README.md` | 概要、方法および使用説明 |

## 適切な利用について

実際の生産計画に適用する前に、作業時間、能力、賃金、割増率、勤務形態およびその他の前提条件を、生産管理、製造技術、人事、安全衛生および法務の各担当者と確認してください。


<img width="2480" height="254" alt="Cabeçalho" src="https://github.com/user-attachments/assets/14f721de-0601-4f8d-9782-06dc02ec0d44" />

<h1 align="center">Previsão do Estado de Saúde do Feto — Tiamat</h1>

<h4 align="center">Isabela Leme Cruz</h4>
<h4 align="center">Turma 26 — 2° Semestre</h4>
<h4 align="center">Ilum Escola de Ciência (CNPEM)</h4>

<p align="justify">
Este projeto, denominado "Tiamat", faz parte da disciplina <strong>Aprendizado de Máquina</strong>, ministrada pelo professor doutor Daniel Roberto Cassar, no 2° semestre de 2026.
</p>

---

<h2>📖 Introdução</h2>

<p align="justify"> A cardiotocografia (CTG) é um exame amplamente utilizado no acompanhamento da gestação e do parto, pois registra continuamente a frequência cardíaca fetal e as contrações uterinas. A interpretação do exame, porém, depende da experiência do profissional e pode variar entre observadores, o que torna relevante o desenvolvimento de métodos capazes de auxiliar na identificação precoce de fetos em risco. Nesse contexto, técnicas de <strong>aprendizado de máquina</strong> podem ser utilizadas para aprender padrões a partir das medidas extraídas do exame e realizar classificações. </p>

<p align="justify"> Este projeto compara cinco algoritmos de classificação na previsão do <strong>estado de saúde fetal</strong> (normal, suspeito ou patológico), utilizando o conjunto de dados <strong>Fetal Health Classification</strong>, disponibilizado no Kaggle a partir de dados de cardiotocografia do UCI Machine Learning Repository. </p>

<h2>📌 Descrição do projeto</h2>

<p align="justify">
O conjunto de dados reúne <strong>2.126 exames</strong> de cardiotocografia, cada um descrito por <strong>21 atributos numéricos</strong> extraídos do registro da frequência cardíaca fetal e das contrações uterinas. O alvo, <code>fetal_health</code>, possui três classes (<strong>normal, suspeito e patológico</strong>), atribuídas por três obstetras especialistas. Após a remoção de 13 linhas duplicadas, restaram <strong>2.113 exames</strong>, com as classes <strong>desbalanceadas</strong>: aproximadamente 77,9% normais, 13,8% suspeitos e 8,3% patológicos.
</p>

<p align="justify">
Por causa desse desbalanceamento, a acurácia sozinha não é uma boa métrica: um modelo que sempre prevê "normal" já acertaria cerca de 78% dos exames sem identificar nenhum caso de risco. Por isso, a avaliação prioriza o <strong>F1-macro</strong>, a <strong>balanced accuracy</strong> e o <strong>recall da classe patológica</strong>, além das matrizes de confusão.
</p>

<p align="justify">
Foram comparados <strong>cinco algoritmos</strong>, de famílias diferentes, além de dois modelos de referência (baselines):
</p>

<ul>
    <li>Regressão logística (modelo linear);</li>
    <li>k-vizinhos mais próximos (k-NN);</li>
    <li>Máquina de vetores de suporte (SVM);</li>
    <li>Random Forest;</li>
    <li>Gradient Boosting (<code>HistGradientBoostingClassifier</code>).</li>
</ul>

<p align="justify">
Cada algoritmo é um <code>Pipeline</code> com normalização (quando necessária), seleção de atributos ou redução de dimensionalidade e o classificador. Todas as escolhas, inclusive as de pré-processamento, são hiperparâmetros otimizados com o <strong>Optuna</strong>, com <strong>100 tentativas por algoritmo</strong>. A comparação usa <strong>validação cruzada estratificada</strong> sobre o conjunto de treino, e o conjunto de teste é usado <strong>uma única vez</strong>, apenas para o modelo escolhido.
</p>

---

<h2>📂 Notebooks e arquivos do projeto</h2>

<ul>
    <li>
        <strong>README.md:</strong> descrição geral, metodologia, resultados e conclusões do projeto.
    </li>
    <li>
        <strong>NOME_DO_NOTEBOOK.ipynb:</strong> notebook contendo toda a análise, desde o carregamento e a exploração dos dados até a otimização, a escolha do modelo final, a avaliação no teste e a explicação do modelo, com textos explicativos ao longo do código.
    </li>
    <li>
        <strong>fetal_health.csv:</strong> conjunto de dados, baixado manualmente do Kaggle e armazenado na mesma pasta do notebook.
    </li>
    <li>
        <strong>tiamat_&lt;algoritmo&gt;.db:</strong> arquivos SQLite gerados pelo Optuna, que guardam as tentativas de cada otimização para que a busca possa ser retomada sem repetir o trabalho.
    </li>
</ul>

---

<h2>⚙️ Funcionamento</h2>

<h3>1. Carregamento, limpeza e descrição dos dados</h3>

<p align="justify">
O arquivo <code>fetal_health.csv</code> é carregado com o pandas. A coluna <code>baseline value</code> foi renomeada para <code>baseline_value</code> (a única com espaço no nome) e o alvo foi convertido de <code>float64</code> para inteiro. Foram identificadas <strong>13 linhas duplicadas</strong>, sem rótulos conflitantes, que foram removidas antes da divisão entre treino e teste para evitar <strong>vazamento de dados</strong>, o que deixou o conjunto com 2.113 exames.
</p>

<h3>2. Análise Exploratória dos Dados (EDA)</h3>

<p align="justify">
Foram analisadas a distribuição do alvo, os histogramas de todos os 21 atributos, a distribuição de 8 atributos de maior relevância clínica (com curva de densidade), a correlação de Pearson entre os atributos, a correlação de Spearman de cada atributo com o alvo e os boxplots dos principais atributos por classe. A EDA mostrou forte assimetria e muitos zeros em vários atributos, redundância entre as medidas do histograma e maior capacidade de separação das classes nos atributos de variabilidade e desaceleração.
</p>

<h3>3. Divisão treino/teste</h3>

<p align="justify">
Os dados foram divididos de forma <strong>estratificada</strong>, com 80% para treino (1.690 exames) e 20% para teste (423 exames), preservando a proporção das classes. O conjunto de teste ficou reservado e só foi usado ao final do trabalho.
</p>

<h3>4. Métricas e modelos de referência</h3>

<p align="justify">
A avaliação usa validação cruzada estratificada de 5 folds, com as mesmas divisões para todos os modelos, e as métricas <strong>F1-macro, balanced accuracy, recall da classe patológica</strong> e acurácia. Os modelos de referência são um <code>DummyClassifier</code> (<code>most_frequent</code>) e uma <strong>regressão logística simples</strong> com <code>StandardScaler</code>, que define o piso a ser superado pelos modelos otimizados.
</p>

<h3>5. Pipelines e espaço de busca</h3>

<p align="justify">
Cada tentativa do Optuna monta um <code>Pipeline</code> com:
</p>

<ul>
    <li><strong>Normalização</strong> (<code>StandardScaler</code>, <code>RobustScaler</code> ou <code>MinMaxScaler</code>), apenas para regressão logística, k-NN e SVM, que são sensíveis à escala;</li>
    <li><strong>Redução de atributos</strong>: nenhuma, RFE (5 a 20 atributos) ou PCA (5 a 21 componentes, oferecido apenas aos modelos com normalização);</li>
    <li><strong>Classificador</strong>, com hiperparâmetros próprios de cada algoritmo e, quando suportado, <code>class_weight</code> para lidar com o desbalanceamento.</li>
</ul>

<p align="justify">
Como todas as etapas ficam dentro do <code>Pipeline</code>, normalização, RFE e PCA são ajustados apenas com os dados de treino de cada fold, evitando vazamento de dados. O atributo <code>histogram_tendency</code> (valores -1, 0 e 1) foi mantido como numérico, por ter ordem natural.
</p>

<h3>6. Otimização de hiperparâmetros com Optuna</h3>

<p align="justify">
A otimização usa o amostrador <strong>TPE</strong> (<code>multivariate=True</code>, com semente fixa), com <strong>100 tentativas por algoritmo</strong>. A função objetivo é o <strong>F1-macro médio</strong> da validação cruzada de 5 folds no treino. Hiperparâmetros que variam em mais de uma ordem de grandeza (como <code>C</code> e a taxa de aprendizado) são sorteados em escala logarítmica, e a primeira tentativa de cada estudo é a configuração padrão do scikit-learn, o que permite medir o ganho real da busca. Também são registradas a convergência de cada tentativa, o histórico de otimização e a importância dos hiperparâmetros.
</p>

<h3>7. Reavaliação e escolha do modelo final</h3>

<p align="justify">
O F1-macro obtido na otimização é o do melhor entre 100 tentativas, o que tende a ser otimista. Por isso, a melhor configuração de cada algoritmo foi reavaliada em uma <strong>validação cruzada repetida (5 folds × 5 repetições = 25 avaliações)</strong>, com semente diferente da usada na busca. O modelo final foi escolhido <strong>exclusivamente</strong> por essa estimativa, sem usar o conjunto de teste.
</p>

<h3>8. Matrizes de confusão de todos os modelos</h3>

<p align="justify">
Para ver onde cada modelo erra, foram geradas matrizes de confusão (em número de exames e em proporção por classe real) dos sete modelos comparados, a partir de previsões de validação cruzada no conjunto de treino, com <code>cross_val_predict</code>. Essas matrizes dão atenção especial ao erro mais grave, classificar um exame patológico como normal.
</p>

<h3>9. Avaliação final no conjunto de teste</h3>

<p align="justify">
O modelo escolhido foi treinado com todo o conjunto de treino e avaliado uma única vez nos exames de teste. Como o teste tem poucos exames patológicos, as métricas vêm acompanhadas de um <strong>intervalo de confiança de 95% por bootstrap</strong> (2.000 sorteios com reposição).
</p>

<h3>10. Explicação do modelo</h3>

<p align="justify">
Para entender em quais atributos o modelo se apoia, foi calculada a <strong>importância por permutação</strong> (30 repetições por atributo), para o F1-macro e para o recall da classe patológica. O resultado foi comparado com a correlação de Spearman da análise exploratória.
</p>

---

<h2>📊 Resultados</h2>

<h3>Modelos de referência (validação cruzada no treino)</h3>

<table align="center">
    <thead>
        <tr>
            <th>Modelo</th>
            <th>Acurácia</th>
            <th>F1-macro</th>
            <th>Balanced accuracy</th>
            <th>Recall patológico</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Dummy (classe mais frequente)</td>
            <td>0,779</td>
            <td>0,292</td>
            <td>0,333</td>
            <td>0,000</td>
        </tr>
        <tr>
            <td>Regressão logística simples</td>
            <td>0,888</td>
            <td>0,779</td>
            <td>0,769</td>
            <td>0,714</td>
        </tr>
    </tbody>
</table>

<h3>Ganho da otimização com Optuna (F1-macro, validação cruzada no treino)</h3>

<table align="center">
    <thead>
        <tr>
            <th>Algoritmo</th>
            <th>Configuração de partida</th>
            <th>Melhor configuração</th>
            <th>Ganho</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Regressão logística</td>
            <td>0,778</td>
            <td>0,808</td>
            <td>0,030</td>
        </tr>
        <tr>
            <td>k-NN</td>
            <td>0,789</td>
            <td>0,865</td>
            <td>0,076</td>
        </tr>
        <tr>
            <td>SVM</td>
            <td>0,821</td>
            <td>0,862</td>
            <td>0,041</td>
        </tr>
        <tr>
            <td>Random Forest</td>
            <td>0,883</td>
            <td>0,894</td>
            <td>0,010</td>
        </tr>
        <tr>
            <td>Gradient Boosting</td>
            <td>0,893</td>
            <td>0,909</td>
            <td>0,016</td>
        </tr>
    </tbody>
</table>

<h3>Reavaliação em validação cruzada repetida (5 × 5)</h3>

<table align="center">
    <thead>
        <tr>
            <th>Modelo</th>
            <th>F1-macro</th>
            <th>Recall patológico</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td><strong>Gradient Boosting (escolhido)</strong></td>
            <td><strong>0,905 ± 0,019</strong></td>
            <td><strong>0,910 ± 0,042</strong></td>
        </tr>
        <tr>
            <td>Random Forest</td>
            <td>0,895 ± 0,026</td>
            <td>—</td>
        </tr>
        <tr>
            <td>Regressão logística otimizada</td>
            <td>0,787</td>
            <td>—</td>
        </tr>
        <tr>
            <td>Regressão logística simples</td>
            <td>0,789</td>
            <td>—</td>
        </tr>
    </tbody>
</table>

<h3>Avaliação final no conjunto de teste — Gradient Boosting</h3>

<table align="center">
    <thead>
        <tr>
            <th>Métrica</th>
            <th>Valor</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>F1-macro</td>
            <td>0,953 (IC 95%: 0,925 a 0,975)</td>
        </tr>
        <tr>
            <td>Balanced accuracy</td>
            <td>0,935</td>
        </tr>
        <tr>
            <td>Recall patológico</td>
            <td>1,000 (35 de 35 exames)</td>
        </tr>
        <tr>
            <td>Patológicos classificados como normais</td>
            <td>0</td>
        </tr>
        <tr>
            <td>Suspeitos classificados como normais</td>
            <td>11</td>
        </tr>
        <tr>
            <td>Normais classificados como suspeitos</td>
            <td>2</td>
        </tr>
    </tbody>
</table>

---

<h2>📚 Bibliotecas utilizadas</h2>

<p align="justify">
O projeto foi desenvolvido em <strong>Python</strong>, utilizando principalmente bibliotecas do ecossistema científico e de aprendizado de máquina.
</p>

<ul>
    <li><strong>pandas (<code>pd</code>) e numpy (<code>np</code>):</strong> carregamento, manipulação e organização dos dados.</li>
    <li><strong>matplotlib.pyplot (<code>plt</code>) e seaborn (<code>sns</code>):</strong> histogramas, boxplots, mapa de calor de correlação, gráficos de importância e matrizes de confusão.</li>
    <li><strong>optuna:</strong> otimização de hiperparâmetros (<code>create_study</code>, <code>TPESampler</code> e gráficos de histórico e de importância dos hiperparâmetros).</li>
    <li><strong>functools.partial:</strong> fixa os argumentos da função objetivo, deixando livre apenas o <code>trial</code> fornecido pelo Optuna.</li>
    <li><strong>train_test_split, StratifiedKFold, RepeatedStratifiedKFold, cross_validate e cross_val_predict:</strong> divisão estratificada e validação cruzada.</li>
    <li><strong>Pipeline e make_pipeline:</strong> encadeiam normalização, redução de atributos e classificador, evitando vazamento de dados.</li>
    <li><strong>StandardScaler, RobustScaler e MinMaxScaler:</strong> estratégias de normalização.</li>
    <li><strong>PCA e RFE:</strong> redução de dimensionalidade e seleção de atributos por eliminação recursiva.</li>
    <li><strong>DummyClassifier, LogisticRegression, KNeighborsClassifier, SVC, RandomForestClassifier e HistGradientBoostingClassifier:</strong> modelos de referência e algoritmos comparados.</li>
    <li><strong>accuracy_score, balanced_accuracy_score, f1_score, recall_score, make_scorer, classification_report, confusion_matrix e ConfusionMatrixDisplay:</strong> cálculo e visualização das métricas.</li>
    <li><strong>permutation_importance:</strong> importância dos atributos para o modelo final.</li>
</ul>

---

<h2>💻 Pré-requisitos e como executar</h2>

<h3>1. Instalação das bibliotecas</h3>

<p align="justify">
Para executar o notebook, é necessário possuir uma instalação do Python e instalar as bibliotecas utilizadas no projeto. O notebook usa <code>l1_ratio</code> na regressão logística (em vez de <code>penalty</code>, descontinuado a partir da versão 1.8 do scikit-learn), portanto é recomendável uma versão recente do scikit-learn.
</p>

```bash
pip install pandas numpy matplotlib seaborn scikit-learn optuna
```

<h3>2. Clonagem do repositório</h3>

```bash
git clone https://github.com/isalemecruz/NOME_DO_REPOSITORIO.git
```

<h3>3. Conjunto de dados</h3>

<p align="justify">
Baixe o arquivo <code>fetal_health.csv</code> em <a href="https://www.kaggle.com/datasets/andrewmvd/fetal-health-classification">kaggle.com/datasets/andrewmvd/fetal-health-classification</a> e coloque-o na mesma pasta do notebook. O download é manual, o que evita a necessidade de autenticação no Kaggle.
</p>

<h3>4. Execução</h3>

<p align="justify">
Abra o notebook em um ambiente compatível com Jupyter Notebook ou JupyterLab e execute as células sequencialmente. A otimização do Optuna (100 tentativas por algoritmo) pode demorar, principalmente para o Gradient Boosting. Como os estudos são salvos em arquivos <code>.db</code>, ao executar novamente o notebook só são feitas as tentativas que faltam.
</p>

---

<h2>🧠 Conclusões e principais aprendizados</h2>

<p align="justify">
Neste trabalho, foram comparados cinco algoritmos de classificação, otimizados com o Optuna, na previsão do estado de saúde fetal a partir de exames de cardiotocografia. Os principais resultados e aprendizados foram:
</p>

<ul>

<li>
<p align="justify">
<strong>A acurácia é enganosa em dados desbalanceados:</strong>
o modelo Dummy obteve acurácia de <strong>0,779</strong>, mas F1-macro de apenas <strong>0,292</strong> e recall patológico igual a zero, pois acertava a classe normal sem identificar nenhum caso de risco.
</p>
</li>

<li>
<p align="justify">
<strong>Os modelos baseados em árvores tiveram o melhor desempenho:</strong>
na reavaliação com validação cruzada repetida, o Gradient Boosting (F1-macro <strong>0,905 ± 0,019</strong>) e o Random Forest (<strong>0,895 ± 0,026</strong>) ficaram à frente dos demais. A diferença de 0,010 entre os dois é menor que o desvio padrão de ambos, de modo que não há evidência clara de vantagem de um sobre o outro. O Gradient Boosting foi escolhido por ter a maior média.
</p>
</li>

<li>
<p align="justify">
<strong>O ganho da otimização variou entre os algoritmos:</strong>
foi maior para o k-NN (de 0,789 para 0,865) e o SVM (de 0,821 para 0,862), e pequeno para os modelos de árvores. Na regressão logística, o ganho aparente (0,778 para 0,808) não se confirmou na reavaliação (0,787 contra 0,789 do modelo simples), o que mostra que parte do resultado da busca é otimismo de seleção e reforça a importância de reavaliar o melhor modelo em divisões novas.
</p>
</li>

<li>
<p align="justify">
<strong>No conjunto de teste, o modelo escolhido identificou todos os casos patológicos:</strong>
foram <strong>35 de 35</strong> exames patológicos (recall 1,000) e F1-macro de <strong>0,953</strong>. Como há poucos exames patológicos no teste, esse valor deve ser visto com cautela: o intervalo de confiança do recall vai de cerca de 0,90 a 1,00, e a validação cruzada indicou um recall médio de 0,910. Os erros se concentraram entre as classes normal e suspeito (11 suspeitos classificados como normais e 2 normais classificados como suspeitos).
</p>
</li>

<li>
<p align="justify">
<strong>O modelo se apoia nos atributos de variabilidade e aceleração:</strong>
na importância por permutação, os atributos mais importantes foram <code>abnormal_short_term_variability</code>, <code>percentage_of_time_with_abnormal_long_term_variability</code> e <code>accelerations</code>, os mesmos que se destacaram na análise exploratória. As duas análises concordam apenas em parte (correlação de Spearman de 0,363 entre os rankings), pois a importância por permutação considera interações e redundância entre os atributos.
</p>
</li>

</ul>

<p align="justify">
De forma geral, o trabalho mostrou que, em um problema desbalanceado e com atributos redundantes, a escolha das métricas, a validação sem vazamento de dados e a reavaliação dos melhores modelos em divisões novas foram tão importantes quanto a escolha do algoritmo.
</p>

---

<h3>🤖 Descrição do uso de Inteligência Artificial</h3>

<p align="justify"> Durante o desenvolvimento do projeto, utilizei o <strong>Claude</strong> como apoio em [descreva as etapas: por exemplo, revisão do notebook, explicação dos hiperparâmetros, matrizes de confusão, formatação das referências]. As sugestões da ferramenta foram analisadas e adaptadas por mim, sendo os códigos executados, os experimentos realizados e os resultados analisados por mim. </p>

<p align="justify"> [Se usou outra ferramenta, como o ChatGPT, descreva aqui o uso em um parágrafo próprio.] </p>

<p align="justify"> <a href="LINK_DO_REGISTRO_DA_CONVERSA">Registro da conversa</a> </p>

---

<h2>🚀 Melhorias futuras</h2>

<p align="justify">
Como possíveis melhorias futuras, seria interessante testar valores intermediários de <code>l1_ratio</code> na regressão logística (<em>elastic net</em>), comparar o desempenho com outros algoritmos de boosting, usar <strong>validação cruzada aninhada</strong> para uma estimativa menos otimista do desempenho dos modelos otimizados e avaliar o modelo em dados de outra instituição, para verificar se o desempenho se mantém.
</p>

<p align="justify">
Também poderia ser explorado o ajuste do <strong>limiar de decisão</strong> ou de pesos de custo diferentes por tipo de erro, já que classificar um exame patológico (ou suspeito) como normal tem consequências clínicas diferentes de um falso alarme.
</p>

---

<h2>📎 Repositório</h2>

<p align="center">
    <a href="https://github.com/isalemecruz/NOME_DO_REPOSITORIO">
        <strong>🔗 Acessar o repositório no GitHub</strong>
    </a>
</p>

<br>

<img width="1664" height="215" alt="Rodapé" src="https://github.com/user-attachments/assets/2f033fd0-b1f5-4720-9625-f158f5e865c6" />

<img width="2480" height="254" alt="Cabeçalho" src="https://github.com/user-attachments/assets/14f721de-0601-4f8d-9782-06dc02ec0d44" />

<h1 align="center">Tiamat: Classificação do Estado de Saúde Fetal com Aprendizado de Máquina</h1>

<h4 align="center">Isabela Leme Cruz</h4>
<h4 align="center">Turma 26 — 2° Semestre</h4>
<h4 align="center">Ilum Escola de Ciência (CNPEM)</h4>

<p align="justify">
Este projeto corresponde ao <strong>projeto final</strong> da disciplina <strong>Aprendizado de Máquina</strong>, denominado "Criatura lendária: Tiamat", ministrada pelo professor doutor Daniel Roberto Cassar, no 2° semestre de 2026.
</p>

---

<h2>📖 Introdução</h2>

<p align="justify">
A <strong>cardiotocografia (CTG)</strong> é um exame que registra a frequência cardíaca fetal e as contrações uterinas, sendo amplamente utilizado no acompanhamento da gestação e do parto para identificar sinais de comprometimento do bem-estar fetal. A interpretação do exame costuma depender da análise de especialistas, o que torna interessante o desenvolvimento de métodos capazes de apoiar essa classificação a partir de medidas extraídas do exame.
</p>

<p align="justify">
Este projeto investiga a aplicação de algoritmos de <strong>aprendizado de máquina</strong> na classificação do estado de saúde fetal em três categorias (<strong>normal, suspeito e patológico</strong>), utilizando o conjunto de dados <strong>Fetal Health Classification</strong>, derivado do conjunto <em>Cardiotocography</em> do UCI Machine Learning Repository.
</p>

---

<h2>📌 Descrição do projeto</h2>

<p align="justify">
O projeto consiste na comparação do desempenho de <strong>cinco algoritmos</strong> de aprendizado de máquina, além de um modelo baseline, aplicados à classificação do estado de saúde fetal. O conjunto original possui <strong>2.126 exames</strong> e <strong>21 atributos</strong> numéricos extraídos de cardiotocografias. Após a remoção de <strong>13 exames duplicados</strong>, restaram <strong>2.113 exames</strong>.
</p>

<p align="justify">
O target, denominado <code>fetal_health</code>, possui três classes fortemente desbalanceadas: aproximadamente <strong>77,9% normal</strong>, <strong>13,8% suspeito</strong> e <strong>8,3% patológico</strong>. Esse desbalanceamento orientou várias decisões do trabalho, como a escolha das métricas, a estratificação das divisões e o uso de pesos de classe na otimização.
</p>

<p align="justify">
Foram comparados os seguintes algoritmos:
</p>

<ul>
    <li>Regressão logística;</li>
    <li>k-vizinhos mais próximos (k-NN);</li>
    <li>Máquina de vetores de suporte (SVM);</li>
    <li>Random Forest;</li>
    <li>Gradient Boosting (<code>HistGradientBoostingClassifier</code>).</li>
</ul>

<p align="justify">
Cada algoritmo é um <code>Pipeline</code> com até três etapas: <strong>normalização</strong> (apenas para os modelos sensíveis à escala), <strong>seleção de atributos ou redução de dimensionalidade</strong> e o <strong>classificador</strong>. Todas essas escolhas foram tratadas como hiperparâmetros e otimizadas com o <strong>Optuna</strong>, com <strong>100 tentativas por algoritmo</strong> (500 no total). O desempenho foi avaliado principalmente pelo <strong>F1-macro</strong>, pela <strong>balanced accuracy</strong> e pelo <strong>recall da classe patológica</strong>, considerando o custo clínico de não identificar um exame de risco.
</p>

---

<h2>📂 Notebooks e arquivos do projeto</h2>

<ul>
    <li>
        <strong>README.md:</strong> descrição geral, metodologia, resultados e conclusões do projeto.
    </li>
    <li>
        <strong>[NOME-DO-NOTEBOOK].ipynb:</strong> notebook contendo toda a análise, desde a exploração dos dados até a otimização, a comparação dos modelos, a explicação do modelo final e as conclusões, com explicações em células de texto ao longo do código.
    </li>
    <li>
        <strong>data/fetal_health.csv:</strong> conjunto de dados utilizado (versão do Kaggle, com 21 atributos e o target <code>fetal_health</code>).
    </li>
    <li>
        <strong>[PASTA-DOS-ESTUDOS-OPTUNA]/:</strong> estudos do Optuna salvos em arquivos <code>.db</code>, permitindo reproduzir os gráficos e os resultados da otimização sem repetir as 500 tentativas.
    </li>
    <li>
        <strong>requirements.txt:</strong> bibliotecas necessárias e suas versões.
    </li>
    <li>
        <strong>.gitignore:</strong> arquivos que não devem ser versionados (caches, arquivos temporários do Jupyter etc.).
    </li>
</ul>

<p align="justify">
<em>[A PREENCHER: se a otimização for feita no HPC, listar aqui também os scripts, o arquivo <code>job.sh</code> do slurm e a saída do slurm. Caso contrário, apague esta linha.]</em>
</p>

---

<h2>⚙️ Funcionamento</h2>

<h3>1. Carregamento e descrição dos dados</h3>

<p align="justify">
O dataset é carregado a partir do arquivo <code>data/fetal_health.csv</code>. A base não possui valores faltantes e todos os atributos são numéricos, entre eles medidas da frequência cardíaca fetal basal, acelerações, desacelerações, variabilidade de curto e longo prazo e estatísticas do histograma da frequência cardíaca.
</p>

<h3>2. Análise Exploratória dos Dados (EDA)</h3>

<p align="justify">
Foram analisados o tamanho e os tipos dos dados, a presença de duplicatas, a distribuição do target, a distribuição dos atributos, as correlações de Pearson entre atributos e as correlações de Spearman com o target, além de boxplots dos atributos por classe.
</p>

<p align="justify">
Entre os principais achados, destacam-se a <strong>forte redundância entre atributos do histograma</strong> (<code>histogram_mean</code>, <code>histogram_median</code> e <code>histogram_mode</code>, além do par <code>histogram_min</code> e <code>histogram_width</code>), a associação positiva do target com a variabilidade anormal de curto prazo (<code>abnormal_short_term_variability</code>), a associação negativa com o número de acelerações (<code>accelerations</code>) e a <strong>ocorrência de desacelerações prolongadas, muito mais frequente nos exames patológicos</strong>.
</p>

<p align="justify">
<em>[A PREENCHER: confira os valores acima com as saídas do seu notebook e ajuste o texto, se necessário.]</em>
</p>

<h3>3. Preparação dos dados e divisão treino/teste</h3>

<p align="justify">
As <strong>13 linhas duplicadas</strong> foram removidas antes da divisão, após verificar que nenhuma delas apresentava rótulos conflitantes. Em seguida, os dados foram divididos em <strong>1.690 exames de treino e 423 de teste</strong>, de forma <strong>estratificada</strong>, preservando a proporção das classes. O conjunto de teste ficou separado e foi utilizado <strong>uma única vez</strong>, apenas na avaliação final.
</p>

<p align="justify">
A normalização, a seleção de atributos e a redução de dimensionalidade ficam <strong>dentro do <code>Pipeline</code></strong> e, portanto, são ajustadas apenas nos dados de treino de cada <em>fold</em> da validação cruzada, evitando <strong>vazamento de dados (data leakage)</strong>. O atributo ordinal <code>histogram_tendency</code> (valores -1, 0 e 1) foi mantido como numérico.
</p>

<h3>4. Métricas de avaliação</h3>

<p align="justify">
Como as classes são desbalanceadas, a acurácia sozinha é enganosa: um modelo que sempre prevê "normal" acerta cerca de 78% dos exames sem identificar nenhum caso de risco. Por isso, foram utilizados o <strong>F1-macro</strong> (métrica otimizada pelo Optuna), a <strong>balanced accuracy</strong>, o <strong>recall da classe patológica</strong>, a acurácia e a <strong>matriz de confusão</strong>.
</p>

<h3>5. Modelo baseline</h3>

<p align="justify">
Foram utilizados dois modelos de referência, avaliados por validação cruzada estratificada de 5 <em>folds</em> no conjunto de treino: um <code>DummyClassifier</code> (classe mais frequente), que estabelece o piso mínimo, e uma <strong>regressão logística simples</strong> com normalização, que representa o desempenho de um modelo sem nenhum ajuste e é o <strong>piso a ser superado</strong> pelos modelos otimizados.
</p>

<h3>6. Algoritmos e espaço de busca</h3>

<p align="justify">
A tabela resume os algoritmos comparados. A justificativa detalhada de cada intervalo está no notebook.
</p>

<table align="center">
    <thead>
        <tr>
            <th>Algoritmo</th>
            <th>Normalização</th>
            <th>Principais hiperparâmetros otimizados</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Regressão logística</td>
            <td>Sim (Standard, Robust ou MinMax)</td>
            <td><code>C</code> (escala log), <code>penalty</code> (l1 ou l2), <code>class_weight</code></td>
        </tr>
        <tr>
            <td>k-NN</td>
            <td>Sim (Standard, Robust ou MinMax)</td>
            <td><code>n_neighbors</code> (1 a 49), <code>weights</code>, <code>p</code> de Minkowski (1 a 2)</td>
        </tr>
        <tr>
            <td>SVM</td>
            <td>Sim (Standard, Robust ou MinMax)</td>
            <td><code>kernel</code> (linear, rbf ou poly), <code>C</code>, <code>gamma</code>, <code>degree</code>, <code>class_weight</code></td>
        </tr>
        <tr>
            <td>Random Forest</td>
            <td>Não</td>
            <td>[A PREENCHER]</td>
        </tr>
        <tr>
            <td>Gradient Boosting</td>
            <td>Não</td>
            <td>[A PREENCHER]</td>
        </tr>
    </tbody>
</table>

<p align="justify">
Em todos os pipelines, o Optuna também escolhe a estratégia de redução: <strong>nenhuma</strong>, <strong>seleção de atributos por RFE</strong> (de 5 a 20 atributos) ou <strong>PCA</strong> (de 5 a 21 componentes, oferecido apenas aos modelos com normalização). Essa escolha foi motivada pela redundância entre atributos observada na análise exploratória.
</p>

<h3>7. Otimização de hiperparâmetros com Optuna</h3>

<p align="justify">
A otimização usa o amostrador <strong>TPE</strong> (<em>Tree-structured Parzen Estimator</em>) do Optuna. A função objetivo é o <strong>F1-macro médio de uma validação cruzada estratificada de 5 <em>folds</em></strong> no conjunto de treino, com <strong>100 tentativas por algoritmo</strong>. Hiperparâmetros que variam em várias ordens de grandeza são sorteados em escala logarítmica, e a primeira tentativa de cada estudo é a configuração padrão do scikit-learn, o que permite verificar se a busca superou o modelo sem ajuste. O conjunto de teste não participa de nenhuma etapa da busca.
</p>

<h3>8. Comparação dos modelos e avaliação final</h3>

<p align="justify">
<em>[A PREENCHER: descreva como os modelos foram comparados (por exemplo, reavaliação da melhor configuração de cada algoritmo com uma nova divisão da validação cruzada), qual modelo foi escolhido e como foi avaliado no conjunto de teste.]</em>
</p>

<h3>9. Explicação do modelo</h3>

<p align="justify">
<em>[A PREENCHER: indique a ferramenta utilizada (permutation importance, SHAP etc.), resuma como ela funciona e quais atributos mais influenciaram o modelo escolhido.]</em>
</p>

---

<h2>📊 Resultados</h2>

<p align="justify">
Resultados da validação cruzada (5 <em>folds</em>) no conjunto de treino, em média ± desvio padrão.
</p>

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
            <td>Baseline: Dummy (classe mais frequente)</td>
            <td>0,779 ± 0,001</td>
            <td>0,292 ± 0,000</td>
            <td>0,333</td>
            <td>0,000</td>
        </tr>
        <tr>
            <td>Baseline: regressão logística simples</td>
            <td>0,888</td>
            <td>0,779 ± 0,027</td>
            <td>0,769</td>
            <td>0,714 ± 0,113</td>
        </tr>
        <tr>
            <td>Regressão logística (Optuna)</td>
            <td>[A PREENCHER]</td>
            <td>[A PREENCHER]</td>
            <td>[A PREENCHER]</td>
            <td>[A PREENCHER]</td>
        </tr>
        <tr>
            <td>k-NN (Optuna)</td>
            <td>[A PREENCHER]</td>
            <td>[A PREENCHER]</td>
            <td>[A PREENCHER]</td>
            <td>[A PREENCHER]</td>
        </tr>
        <tr>
            <td>SVM (Optuna)</td>
            <td>[A PREENCHER]</td>
            <td>[A PREENCHER]</td>
            <td>[A PREENCHER]</td>
            <td>[A PREENCHER]</td>
        </tr>
        <tr>
            <td>Random Forest (Optuna)</td>
            <td>[A PREENCHER]</td>
            <td>[A PREENCHER]</td>
            <td>[A PREENCHER]</td>
            <td>[A PREENCHER]</td>
        </tr>
        <tr>
            <td>Gradient Boosting (Optuna)</td>
            <td>[A PREENCHER]</td>
            <td>[A PREENCHER]</td>
            <td>[A PREENCHER]</td>
            <td>[A PREENCHER]</td>
        </tr>
    </tbody>
</table>

<p align="justify">
<strong>Modelo final no conjunto de teste:</strong> [A PREENCHER: modelo escolhido e suas métricas no teste, avaliado uma única vez].
</p>

---

<h2>📚 Bibliotecas utilizadas</h2>

<p align="justify">
O projeto foi desenvolvido em <strong>Python</strong>, utilizando principalmente bibliotecas do ecossistema científico e de aprendizado de máquina.
</p>

<ul>
    <li><strong>numpy (<code>np</code>) e pandas (<code>pd</code>):</strong> operações numéricas, carregamento e manipulação dos dados.</li>
    <li><strong>matplotlib.pyplot (<code>plt</code>) e seaborn (<code>sns</code>):</strong> visualizações, como histogramas, boxplots, mapas de calor e gráficos de comparação dos modelos.</li>
    <li><strong>scikit-learn:</strong> divisão treino/teste, validação cruzada, normalizadores, PCA, RFE, os classificadores, o <code>Pipeline</code> e as métricas.</li>
    <li><strong>optuna:</strong> otimização de hiperparâmetros e gráficos de acompanhamento da busca.</li>
    <li><strong>[A PREENCHER: shap, caso utilize]:</strong> explicação do modelo final.</li>
</ul>

---

<h2>💻 Pré-requisitos e como executar</h2>

<h3>1. Clonagem do repositório</h3>

```bash
git clone https://github.com/isalemecruz/[NOME-DO-REPOSITORIO].git
cd [NOME-DO-REPOSITORIO]
```

<h3>2. Instalação das bibliotecas</h3>

<p align="justify">
É necessário possuir uma instalação do Python. Instale as bibliotecas a partir do arquivo de requisitos:
</p>

```bash
pip install -r requirements.txt
```

<h3>3. Execução</h3>

<p align="justify">
Abra o notebook em um ambiente compatível com Jupyter Notebook ou JupyterLab e execute as células sequencialmente, de cima para baixo. Os estudos do Optuna estão salvos na pasta indicada acima, então o notebook carrega os resultados da otimização em vez de repeti-la. <em>[A PREENCHER: confirme que o notebook funciona dessa forma e, se quiser permitir refazer a otimização, explique como e quanto tempo leva.]</em>
</p>

---

<h2>🧠 Conclusões e principais aprendizados</h2>

<p align="justify">
<em>[A PREENCHER depois de obter os resultados. Sugestão de tópicos, seguindo o formato do Cubo Gelatinoso:]</em>
</p>

<ul>
    <li><strong>Efeito do desbalanceamento nas métricas:</strong> [o que o baseline Dummy mostrou sobre a acurácia];</li>
    <li><strong>Qual algoritmo teve o melhor desempenho e por quê:</strong> [comparação dos cinco algoritmos];</li>
    <li><strong>Efeito da seleção de atributos e da redução de dimensionalidade:</strong> [o que o Optuna escolheu e se ajudou];</li>
    <li><strong>O que o Optuna trouxe em relação à configuração padrão:</strong> [ganho obtido];</li>
    <li><strong>O que o modelo aprendeu:</strong> [atributos mais importantes e se isso faz sentido clínico];</li>
    <li><strong>Desempenho na classe patológica:</strong> [recall e tipos de erro mais frequentes].</li>
</ul>

<h3>Limitações</h3>

<ul>
    <li>A documentação do dataset não deixa claro se há mais de um exame da mesma gestante. Se houver, a validação cruzada comum pode ter vazamento leve entre os <em>folds</em>;</li>
    <li>Foram removidos 13 exames duplicados, o que reduz levemente o tamanho do conjunto;</li>
    <li>Os rótulos foram definidos por especialistas, e o desempenho do modelo é limitado pela qualidade dessa classificação;</li>
    <li>As classes suspeito e patológico têm poucos exemplos, o que aumenta a variabilidade das métricas nessas classes.</li>
</ul>

---

<h3>🤖 Descrição do uso de Inteligência Artificial</h3>

<p align="justify">
Durante o desenvolvimento do projeto, utilizei o <strong>Claude</strong> como apoio na busca e na verificação do dataset em relação aos requisitos do enunciado, no planejamento das etapas (cronograma), na revisão do notebook (identificação de erros de código, trechos copiados do trabalho anterior e pendências) e na elaboração deste README. As sugestões foram analisadas e adaptadas por mim, e os códigos foram executados e os resultados analisados por mim.
</p>

<p align="justify">
<em>[A PREENCHER E CONFERIR: ajuste este texto ao que você realmente fez, incluindo outras ferramentas, se usou, e o que cada uma gerou (texto, código, revisão). Anexe o registro das conversas (link compartilhado ou arquivo) e cite-o aqui.]</em>
</p>

---

<h2>🚀 Melhorias futuras</h2>

<ul>
    <li>Se houver identificador da gestante, usar validação cruzada agrupada para evitar vazamento entre exames da mesma paciente;</li>
    <li>Ajustar o limiar de decisão para priorizar o recall da classe patológica;</li>
    <li>Calibrar as probabilidades do modelo final;</li>
    <li>Testar outros algoritmos e outras estratégias de tratamento do desbalanceamento (por exemplo, reamostragem dentro da validação cruzada);</li>
    <li>Validar o modelo em um conjunto de dados externo.</li>
</ul>

---

<h2>📚 Referências</h2>

<ol>
    <li>AYRES-DE-CAMPOS, D.; BERNARDES, J.; GARRIDO, A.; MARQUES-DE-SÁ, J.; PEREIRA-LEITE, L. SisPorto 2.0: a program for automated analysis of cardiotocograms. <em>Journal of Maternal-Fetal Medicine</em>, v. 9, n. 5, p. 311–318, 2000.</li>
    <li>KAGGLE. <em>Fetal Health Classification</em>. Disponível em: https://www.kaggle.com/datasets/andrewmvd/fetal-health-classification. Acesso em: [A PREENCHER: data de acesso].</li>
    <li>PEDREGOSA, F. et al. Scikit-learn: Machine Learning in Python. <em>Journal of Machine Learning Research</em>, v. 12, p. 2825–2830, 2011.</li>
    <li>AKIBA, T. et al. Optuna: A Next-generation Hyperparameter Optimization Framework. In: <em>Proceedings of the 25th ACM SIGKDD International Conference on Knowledge Discovery and Data Mining</em>, 2019.</li>
    <li>BERGSTRA, J. et al. Algorithms for Hyper-Parameter Optimization. In: <em>Advances in Neural Information Processing Systems (NIPS)</em>, 2011.</li>
    <li>GUYON, I. et al. Gene selection for cancer classification using support vector machines. <em>Machine Learning</em>, v. 46, p. 389–422, 2002.</li>
    <li>WASKOM, M. L. seaborn: statistical data visualization. <em>Journal of Open Source Software</em>, v. 6, n. 60, p. 3021, 2021.</li>
</ol>

<p align="justify">
A lista completa de referências, com as citações ao longo do texto, está na seção "Referências" do notebook.
</p>

---

<h2>📎 Repositório</h2>

<p align="center">
    <a href="https://github.com/isalemecruz/[NOME-DO-REPOSITORIO]">
        <strong>🔗 Acessar o repositório no GitHub</strong>
    </a>
</p>

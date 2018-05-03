
<!--
    As of 2017/10/06, this 'include' file is meant to replace the first paragraph of plain text that is duplicated at the top inside every tutorial-*.md article in azure-docs-pr/articles/data-factory/.

    This 'include' file is basically one paragraph.
    It explains what Azure Data Factory is, to someone who knows nothing about ADF.
-->

Azure Data Factory je služba pro integraci dat. Umožňuje vytvářet pracovní postupy řízené daty v cloudu. Pracovní postup se implementuje jako jeden nebo několik *kanálů*. Kanály orchestrují a automatizují přesuny dat a transformace dat. Kanály můžou provádět s daty následující postupy:

1. Ingestování dat z různorodých datových úložišť
2. Transformování nebo zpracování dat pomocí výpočetních služeb, jako jsou následující:
    - Azure HDInsight Hadoop
    - Spark
    - Azure Data Lake Analytics
    - Azure Machine Learning
3. Publikování výstupních dat do úložišť.
    - Cíl publikování může být Azure SQL Data Warehouse, aby se data mohla využit v aplikacích business intelligence (BI). 

Kanály můžete plánovat pomocí služby Data Factory.


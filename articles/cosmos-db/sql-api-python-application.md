---
title: Kurz vývoje webové aplikace Python Flask pro službu Azure Cosmos DB
description: Projděte si databázový kurz na téma, jak pomocí služby Azure Cosmos DB ukládat data a přistupovat k nim z webové aplikace Python Flask hostované v Azure. Naleznete zde řešení pro vývoj aplikací.
keywords: Vývoj aplikací, python flask, webová aplikace python, vývoj pro web python
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: python
ms.topic: tutorial
ms.date: 02/23/2017
ms.author: sngun
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a2c7b7c5dc68bb96e0b54dc89f2c170cdb20c792
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53081900"
---
# <a name="build-a-python-flask-web-application-using-azure-cosmos-db"></a>Sestavení webové aplikace Python Flask využívající službu Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Tento kurz ukazuje, jak pomocí služby Azure Cosmos DB ukládat data a přistupovat k nim z webové aplikace Python Flask hostované ve službě Azure App Service. V tomto kurzu se předpokládá, že již máte zkušenosti s používáním Pythonu a webů Azure.

Tento databázový kurz se zabývá:

1. Vytvořením a zřízením účtu služby Azure Cosmos DB
2. Vytvořením aplikace Python Flask
3. Připojením ke službě Azure Cosmos DB a jejím používáním z webové aplikace
4. Nasazením webové aplikace do služby Azure App Service

Podle postupu uvedeného v tomto kurzu sestavíte jednoduchou hlasovací aplikaci, která vám umožní hlasovat v anketě.

![Snímek obrazovky hlasovací aplikace vytvořené v tomto databázovém kurzu](./media/sql-api-python-application/cosmos-db-pythonr-run-application.png)

## <a name="database-tutorial-prerequisites"></a>Předpoklady pro databázový kurz
Než budete postupovat podle pokynů tohoto článku, měli byste se ujistit, že máte následující:

* [Předplatné Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Visual Studio 2017](https://www.visualstudio.com/downloads/) s povoleným **vývojem pro Azure** a **vývojem v Pythonu**. V místně otevřeném **Instalačním programu pro Visual Studio** můžete zkontrolovat, jestli jsou tyto požadované součásti nainstalované, a případně je nainstalovat.   
* [Microsoft Azure SDK pro Python 2.7](https://azure.microsoft.com/downloads/) 
* [Python 2.7](https://www.python.org/downloads/windows/). Můžete použít 32bitovou nebo 64bitovou instalaci.

> [!IMPORTANT]
> Pokud Python 2.7 instalujete poprvé, je zapotřebí na obrazovce Customize Python 2.7.13 (Přizpůsobit Python 2.7.13) vybrat **Add python.exe to Path** (Přidat python.exe do proměnné PATH).
> 
> ![Snímek obrazovky Customize Python 2.7.11 (Přizpůsobit Python 2.7.11), kde je nutné vybrat možnost Add python.exe to Path (Přidat python.exe do proměnné PATH)](./media/sql-api-python-application/cosmos-db-python-install.png)
> 
> 

* [Microsoft Visual C++ Compiler pro Python 2.7](https://www.microsoft.com/en-us/download/details.aspx?id=44266).

## <a name="step-1-create-an-azure-cosmos-db-database-account"></a>Krok 1: Vytvoření účtu databáze Azure Cosmos DB
Začněme vytvořením účtu služby Azure Cosmos DB. Pokud již účet máte nebo pokud používáte pro účely tohoto kurzu emulátor služby Azure Cosmos DB, můžete přeskočit na [Krok 2: Vytvoření nové webové aplikace Python Flask](#step-2-create-a-new-python-flask-web-application).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<br/>
Teď si projdeme, jak od základů vytvořit novou webovou aplikaci Python Flask.

## <a name="step-2-create-a-new-python-flask-web-application"></a>Krok 2: Vytvoření nové webové aplikace Python Flask
1. V nástroji Visual Studio najeďte myší v nabídce **Soubor** na **Nový** a klikněte na **Projekt**.
   
    Zobrazí se dialogové okno **Nový projekt**.
2. V levém podokně rozbalte **Šablony**, pak **Python** a klikněte na **Web**. 
3. Ve středním podokně vyberte **Webový projekt Flask**, do pole **Název** zadejte **tutorial** a klikněte na **OK**. Nezapomeňte, že názvy balíčků Python by měly být celé malými písmeny, jak je popsáno v [průvodci správným stylem pro kód Python](https://www.python.org/dev/peps/pep-0008/#package-and-module-names).
   
    Je-li pro vás Python Flask nový, je to rozhraní pro vývoj webových aplikací, které urychluje tvorbu webových aplikací v Pythonu.
   
    ![Snímek obrazovky okna Nový projekt v nástroji Visual Studio se zvýrazněným Pythonem vlevo, vybraným webovým projektem Python Flask uprostřed a názvem tutorial v poli Název](./media/sql-api-python-application/image9.png)
4. V okně **Python Tools for Visual Studio** klikněte na **Nainstalovat do virtuálního prostředí**. 
   
    ![Snímek obrazovky databázového kurzu – okno Python Tools for Visual Studio](./media/sql-api-python-application/python-install-virtual-environment.png)
5. V okně **Přidat virtuální prostředí** v poli Vybrat interpret vyberte Python 2.7 nebo Python 3.5, přijměte ostatní výchozí hodnoty a pak klikněte na **Vytvořit**. Tím se pro projekt nastaví požadované virtuální prostředí Python.
   
    ![Snímek obrazovky databázového kurzu – okno Python Tools for Visual Studio](./media/sql-api-python-application/image10_A.png)
   
    Jakmile je prostředí úspěšně nainstalováno, ve výstupním okně se zobrazí `Successfully installed Flask-0.10.1 Jinja2-2.8 MarkupSafe-0.23 Werkzeug-0.11.5 itsdangerous-0.24 'requirements.txt' was installed successfully.`.

## <a name="step-3-modify-the-python-flask-web-application"></a>Krok 3: Úprava webové aplikace Python Flask
### <a name="add-the-python-flask-packages-to-your-project"></a>Přidání balíčků Python Flask do projektu
Po nastavení projektu bude nutné do projektu přidat požadované balíčky Flask, včetně pydocumentdb, tedy balíčku Python pro rozhraní SQL API služby Azure Cosmos DB.

1. V Průzkumníkovi řešení otevřete soubor s názvem **requirements.txt** a nahraďte jeho obsah následujícím kódem:
   
        flask==0.9
        flask-mail==0.7.6
        sqlalchemy==0.7.9
        flask-sqlalchemy==0.16
        sqlalchemy-migrate==0.7.2
        flask-whooshalchemy==0.55a
        flask-wtf==0.8.4
        pytz==2013b
        flask-babel==0.8
        flup
        pydocumentdb>=1.0.0
2. Uložte soubor **requirements.txt**. 
3. V Průzkumníkovi řešení klikněte pravým tlačítkem na **env** a pak levým na **Nainstalovat z requirements.txt**.
   
    ![Snímek obrazovky, na kterém je zvoleno env (Python 2.7) a v seznamu je zvýrazněna možnost Instalovat z requirements.txt](./media/sql-api-python-application/cosmos-db-python-install-from-requirements.png)
   
    Po úspěšné instalaci se ve výstupním okně zobrazí následující:
   
        Successfully installed Babel-2.3.2 Tempita-0.5.2 WTForms-2.1 Whoosh-2.7.4 blinker-1.4 decorator-4.0.9 flask-0.9 flask-babel-0.8 flask-mail-0.7.6 flask-sqlalchemy-0.16 flask-whooshalchemy-0.55a0 flask-wtf-0.8.4 flup-1.0.2 pydocumentdb-1.6.1 pytz-2013b0 speaklater-1.3 sqlalchemy-0.7.9 sqlalchemy-migrate-0.7.2
   
   > [!NOTE]
   > Ve výjimečných případech se ve výstupním okně může zobrazit informace o selhání. Pokud se to stane, podívejte se, jestli chyba nesouvisí s vyčištěním. Někdy se vyčištění nepovede, ale instalace je přesto úspěšná (posuňte se ve výstupním okně nahoru, kde to je možné ověřit). Instalaci můžete zkontrolovat [ověřením virtuálního prostředí](#verify-the-virtual-environment). Pokud se instalace nepovedla, ale ověření proběhlo úspěšně, můžete pokračovat.
   > 
   > 

### <a name="verify-the-virtual-environment"></a>Ověření virtuálního prostředí
Ujistěme se, že se vše správně nainstalovalo.

1. Sestavte řešení stisknutím **CTRL**+**SHIFT**+**B**.
2. Po úspěšném sestavení spusťte web klávesou **F5**. Tímto se spustí vývojový server Flask a webový prohlížeč. Měla by se zobrazit následující stránka:
   
    ![Prázdný webový vývojový projekt Python Flask zobrazený v prohlížeči](./media/sql-api-python-application/image12.png)
3. Klávesami **SHIFT**+**F5** v nástroji Visual Studio ukončete ladění webu.

### <a name="create-database-collection-and-document-definitions"></a>Vytvoření definic databáze, kolekcí a dokumentů
Nyní vytvořte hlasovací aplikaci tak, že přidáme nové soubory a jiné aktualizujeme.

1. V Průzkumníkovi řešení klikněte pravým tlačítkem na projekt **tutorial**, pak levým na **Přidat** a nakonec také levým na **Nová položka**. Vyberte **Prázdný soubor Python** a pojmenujte jej **forms.py**.  
2. Do souboru forms.py přidejte následující kód a soubor uložte.

```python
from flask_wtf import Form
from wtforms import RadioField

class VoteForm(Form):
    deploy_preference  = RadioField('Deployment Preference', choices=[
        ('Web Site', 'Web Site'),
        ('Cloud Service', 'Cloud Service'),
        ('Virtual Machine', 'Virtual Machine')], default='Web Site')
```


### <a name="add-the-required-imports-to-viewspy"></a>Přidání požadovaných importů do views.py
1. V Průzkumníkovi řešení rozbalte složku **tutorial** a otevřete soubor **views.py**. 
2. Do horní části souboru **views.py** přidejte následující příkazy import a soubor uložte. Tyto příkazu importují balíčky Flask a PythonSDK pro službu Azure Cosmos DB.
   
    ```python
    from forms import VoteForm
    import config_cosmos
    import pydocumentdb.document_client as document_client
    ```

### <a name="create-database-collection-and-document"></a>Vytvoření databáze, kolekce a dokumentu
* Na konec téhož souboru **views.py** přidejte následující kód. Ten vytvoří databázi, která se použije ve formuláři. V souboru **views.py** neodstraňujte žádný kód, který tam již je. Pouze nový kód přidejte na konec.

```python
@app.route('/create')
def create():
    """Renders the contact page."""
    client = document_client.DocumentClient(config_cosmos.COSMOSDB_HOST, {'masterKey': config_cosmos.COSMOSDB_KEY})

    # Attempt to delete the database.  This allows this to be used to recreate as well as create
    try:
        db = next((data for data in client.ReadDatabases() if data['id'] == config_cosmos.COSMOSDB_DATABASE))
        client.DeleteDatabase(db['_self'])
    except:
        pass

    # Create database
    db = client.CreateDatabase({ 'id': config_cosmos.COSMOSDB_DATABASE })

    # Create collection
    collection = client.CreateCollection(db['_self'],{ 'id': config_cosmos.COSMOSDB_COLLECTION })

    # Create document
    document = client.CreateDocument(collection['_self'],
        { 'id': config_cosmos.COSMOSDB_DOCUMENT,
          'Web Site': 0,
          'Cloud Service': 0,
          'Virtual Machine': 0,
          'name': config_cosmos.COSMOSDB_DOCUMENT 
        })

    return render_template(
       'create.html',
        title='Create Page',
        year=datetime.now().year,
        message='You just created a new database, collection, and document.  Your old votes have been deleted')
```


### <a name="read-database-collection-document-and-submit-form"></a>Čtení databáze, kolekce a dokumentu a odeslání formuláře
* Na konec téhož souboru **views.py** přidejte následující kód. Tento kód má na starosti nastavení formuláře a čtení databáze, kolekce a dokumentu. V souboru **views.py** neodstraňujte žádný kód, který tam již je. Pouze nový kód přidejte na konec.

```python
@app.route('/vote', methods=['GET', 'POST'])
def vote(): 
    form = VoteForm()
    replaced_document ={}
    if form.validate_on_submit(): # is user submitted vote  
        client = document_client.DocumentClient(config_cosmos.COSMOSDB_HOST, {'masterKey': config_cosmos.COSMOSDB_KEY})

        # Read databases and take first since id should not be duplicated.
        db = next((data for data in client.ReadDatabases() if data['id'] == config_cosmos.COSMOSDB_DATABASE))

        # Read collections and take first since id should not be duplicated.
        coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config_cosmos.COSMOSDB_COLLECTION))

        # Read documents and take first since id should not be duplicated.
        doc = next((doc for doc in client.ReadDocuments(coll['_self']) if doc['id'] == config_cosmos.COSMOSDB_DOCUMENT))

        # Take the data from the deploy_preference and increment our database
        doc[form.deploy_preference.data] = doc[form.deploy_preference.data] + 1
        replaced_document = client.ReplaceDocument(doc['_self'], doc)

        # Create a model to pass to results.html
        class VoteObject:
            choices = dict()
            total_votes = 0

        vote_object = VoteObject()
        vote_object.choices = {
            "Web Site" : doc['Web Site'],
            "Cloud Service" : doc['Cloud Service'],
            "Virtual Machine" : doc['Virtual Machine']
        }
        vote_object.total_votes = sum(vote_object.choices.values())

        return render_template(
            'results.html', 
            year=datetime.now().year, 
            vote_object = vote_object)

    else :
        return render_template(
            'vote.html', 
            title = 'Vote',
            year=datetime.now().year,
            form = form)
```


### <a name="create-the-html-files"></a>Vytvoření souborů HTML
1. V Průzkumníku řešení klikněte pravým tlačítkem ve složce **tutorial** na složku **templates**, pak levým na **Přidat** a nakonec také levým na **Nová položka**. 
2. Vyberte **Stránka HTML** do pole název zadejte **create.html**. 
3. Opakováním kroků 1 a 2 vytvořte dva další soubory HTML: results.html a vote.html.
4. Do souboru **create.html** do elementu `<body>` přidejte následující kód. Tento kód zobrazí zprávu, že jsme vytvořili novou databázi, kolekci a dokument.
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>{{ title }}.</h2>
    <h3>{{ message }}</h3>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}
    ```
5. Do souboru **results.html** do elementu `<body` přidejte následující kód. Slouží k zobrazení výsledků ankety.
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>Results of the vote</h2>
        <br />
   
    {% for choice in vote_object.choices %}
    <div class="row">
        <div class="col-sm-5">{{choice}}</div>
            <div class="col-sm-5">
                <div class="progress">
                    <div class="progress-bar" role="progressbar" aria-valuenow="{{vote_object.choices[choice]}}" aria-valuemin="0" aria-valuemax="{{vote_object.total_votes}}" style="width: {{(vote_object.choices[choice]/vote_object.total_votes)*100}}%;">
                                {{vote_object.choices[choice]}}
                </div>
            </div>
            </div>
    </div>
    {% endfor %}
   
    <br />
    <a class="btn btn-primary" href="{{ url_for('vote') }}">Vote again?</a>
    {% endblock %}
    ```
6. Do souboru **vote.html** do elementu `<body` přidejte následující kód. Zobrazuje anketu a přijímá hlasy. Při registraci hlasů se ovládací prvek předává do souboru views.py, kde služba Azure Cosmos DB rozpozná udělené hlasy a patřičně připojí dokument.
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>What is your favorite way to host an application on Azure?</h2>
    <form action="" method="post" name="vote">
        {{form.hidden_tag()}}
            {{form.deploy_preference}}
            <button class="btn btn-primary" type="submit">Vote</button>
    </form>
    {% endblock %}
    ```
7. Ve složce **šablony** nahraďte obsah souboru **index.html** následujícím obsahem. Tato stránka slouží jako cílová stránka aplikace.
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>Python + Azure Cosmos DB Voting Application.</h2>
    <h3>This is a sample Cosmos DB voting application using PyDocumentDB</h3>
    <p><a href="{{ url_for('create') }}" class="btn btn-primary btn-large">Create/Clear the Voting Database &raquo;</a></p>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}
    ```

### <a name="add-a-configuration-file-and-change-the-initpy"></a>Přidejte konfigurační soubor a změňte \_\_init\_\_.py.
1. V Průzkumníku řešení klikněte pravým tlačítkem na projekt **tutorial**, pak levým na **Přidat**, dále na **Nová položka**, vyberte **Prázdný soubor Python** a pojmenujte jej **config_cosmos.py**. Formuláře ve Flasku vyžadují tento konfigurační soubor. Můžete jej použít i k poskytnutí tajného klíče. Ten ale pro tento kurz není nezbytný.
2. Do souboru config_cosmos.py přidejte následující kód. V dalším kroku bude nutné upravit hodnoty **COSMOSDB\_HOST** a **COSMOSDB\_KEY**.
   
    ```python
    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
   
    COSMOSDB_HOST = 'https://YOUR_COSMOSDB_NAME.documents.azure.com:443/'
    COSMOSDB_KEY = 'YOUR_SECRET_KEY_ENDING_IN_=='
   
    COSMOSDB_DATABASE = 'voting database'
    COSMOSDB_COLLECTION = 'voting collection'
    COSMOSDB_DOCUMENT = 'voting document'
    ```
3. Na webu [Azure Portal](https://portal.azure.com/) přejděte na stránku **Klíče** tak, že kliknete na **Procházet**, **Účty služby Azure Cosmos DB**, dvakrát kliknete na název účtu, který chcete použít, a v oblasti **Základy** kliknete na tlačítko **Klíče**. Na stránce **Klíče** zkopírujte hodnotu **URI** a vložte ji do souboru **config_cosmos.py** jako hodnotu vlastnosti **COSMOSDB\_HOST**. 
4. Zpátky na webu Azure Portal na stránce **Klíče** zkopírujte hodnotu **Primární klíč** nebo **Sekundární klíč** a vložte ji do souboru **config_cosmos.py** jako hodnotu vlastnosti **COSMOSDB\_KEY**.
5. Do souboru **\_\_init\_\_.py** přidejte následující řádky, které zajistí čtení konfiguračního souboru a základní protokolování: 
   
        app.config.from_object('config_cosmos')
        logging.basicConfig(level=logging.INFO,format='%(asctime)s - %(name)s - %(levelname)s - %(message)s')
        logger = logging.getLogger(__name__)
   
    Obsah souboru tak bude následující:
   
    ```python
    import logging
    from flask import Flask
    app = Flask(__name__)
    app.config.from_pyfile('config_cosmos')
    logging.basicConfig(level=logging.INFO,format='%(asctime)s - %(name)s - %(levelname)s - %(message)s')
    logger = logging.getLogger(__name__)

    import tutorial.views
    ```
6. Po přidání všech souborů by Průzkumník řešení měl vypadat takto:
   
    ![Snímek obrazovky okna Průzkumníka řešení v nástroji Visual Studio](./media/sql-api-python-application/cosmos-db-python-solution-explorer.png)

## <a name="step-4-run-your-web-application-locally"></a>Krok 4: Místní spuštění webové aplikace
1. Sestavte řešení stisknutím **CTRL**+**SHIFT**+**B**.
2. Po úspěšném sestavení spusťte web klávesou **F5**. Na obrazovce byste měli vidět následující.
   
    ![Snímek obrazovky hlasovací aplikace vytvořené pomocí Pythonu a služby Azure Cosmos DB zobrazené ve webovém prohlížeči](./media/sql-api-python-application/cosmos-db-pythonr-run-application.png)
3. Klikněte na **Create/Clear the Voting Database** (Vytvořit nebo vyčistit hlasovací databázi), aby se vygenerovala databáze.
   
    ![Snímek obrazovky webové aplikace a její stránky Vytvořit – podrobnosti o vývoji](./media/sql-api-python-application/cosmos-db-python-run-create-page.png)
4. Pak vyberte svou možnost kliknutím na **Vote** (Hlasovat).
   
    ![Snímek obrazovky webové aplikace se zobrazenou otázkou k hlasování](./media/sql-api-python-application/cosmos-db-vote.png)
5. Každý hlas, který udělíte, navýší příslušný čítač.
   
    ![Snímek obrazovky se zobrazenou stránkou Results of the vote (Výsledek hlasování)](./media/sql-api-python-application/cosmos-db-voting-results.png)
6. Kombinací kláves SHIFT+F5 ukončete ladění projektu.

## <a name="step-5-deploy-the-web-application-to-azure"></a>Krok 5: Nasazení webové aplikace do Azure
Když teď máte hotovou aplikaci, která správně funguje místně se službou Azure Cosmos DB, vytvoříme soubor web.config, aktualizujeme soubory na serveru tak, aby odpovídaly místnímu prostředí, a pak zobrazíme dokončenou aplikaci v Azure. Tento postup je specifický pro sadu Visual Studio 2017. Pokud používáte jinou verzi sady Visual Studio, přečtěte si téma [Publikování v Azure App Service](/visualstudio/python/publishing-to-azure).

1. V **Průzkumníku řešení** sady Visual Studio klikněte pravým tlačítkem na projekt a vyberte **Přidat > Nová položka**. V dialogovém okně, které se zobrazí, vyberte šablonu **web.config pro Azure (FastCGI)** a pak vyberte **OK**. Tím se v kořenovém adresáři vašeho projektu vytvoří soubor `web.config`. 

2. V souboru `web.config` upravte část `<system.webServer>` tak, aby cesta odpovídala instalaci Pythonu. Například pro Python 2.7 x64 by tato položka měla vypadat následovně:
    
    ```xml
    <system.webServer>
        <handlers>
            <add name="PythonHandler" path="*" verb="*" modules="FastCgiModule" scriptProcessor="D:\home\Python27\python.exe|D:\home\Python27\wfastcgi.py" resourceType="Unspecified" requireAccess="Script"/>
        </handlers>
    </system.webServer>
    ```

3. V souboru `web.config` nastavte položku `WSGI_HANDLER` na hodnotu `tutorial.app`, která odpovídá názvu vašeho projektu. 

    ```xml
    <!-- Flask apps only: change the project name to match your app -->
    <add key="WSGI_HANDLER" value="tutorial.app"/>
    ```

4. V **Průzkumníku řešení** sady Visual Studio rozbalte složku **tutorial**, klikněte pravým tlačítkem na složku `static`, vyberte **Přidat > Nová položka...**, vyberte šablonu web.config pro statické soubory Azure a pak vyberte **OK**. Tato akce ve složce `static` vytvoří další soubor `web.config`, který pro tuto složku zakáže zpracování Pythonu. Tato konfigurace odesílá požadavky na statické soubory na výchozí webový server, a ne do aplikace Python.

5. Uložte soubory, pak klikněte pravým tlačítkem na projekt v Průzkumníku řešení (ujistěte se, že aplikace již není spuštěná místně) a vyberte **Publikovat**.  
   
     ![Snímek obrazovky projektu tutorial vybraného v Průzkumníkovi řešení se zvýrazněnou možností Publikovat](./media/sql-api-python-application/image20.png)
6. V dialogovém okně **Publikovat** vyberte **Microsoft Azure App Service**, pak vyberte **Vytvořit novou** a klikněte na **Publikovat**.
   
    ![Snímek obrazovky okna Publikování webu se zvýrazněnou možností Microsoft Azure App Service](./media/sql-api-python-application/cosmos-db-python-publish.png)
7. V dialogovém okně **Vytvořit plán App Service** zadejte název vaší webové aplikace a vaše **předplatné**, **skupinu prostředků** a **plán služby App Service**, a pak klikněte na **Vytvořit**.
   
    ![Snímek obrazovky okna Okno Microsoft Azure Web Apps](./media/sql-api-python-application/cosmos-db-python-create-app-service.png)
8. Během několika sekund sada Visual Studio dokončí kopírování souborů na server a zobrazí zprávu „The page cannot be displayed because an internal server error has occurred“ (Stránku není možné zobrazit, protože došlo k interní chybě serveru) na stránce `http://<your app service>.azurewebsites.net/`.

9. Na webu Azure Portal otevřete svůj nový účet služby App Service, pak se v navigační nabídce posuňte dolů do části **Vývojové nástroje**, vyberte **Rozšíření** a klikněte na **+ Přidat**.

10. Na stránce **Zvolit rozšíření** se posuňte dolů k nejnovější instalaci Pythonu 2.7, vyberte možnost x86 nebo x64 a pak kliknutím na **OK** přijměte právní podmínky.  
   
11. Pomocí konzoly Kudu, kterou najdete na adrese `https://<your app service name>.scm.azurewebsites.net/DebugConsole`, nainstalujte balíčky uvedené v souboru `requirements.txt` vaší aplikace. Uděláte to tak, že v konzole pro diagnostiku Kudu přejdete do složky Pythonu `D:\home\Python27` a pak spustíte následující příkaz, jak je popsáno v části věnované [konzole Kudu](/visualstudio/python/managing-python-on-azure-app-service#azure-app-service-kudu-console):

    ```
    D:\home\Python27>python -m pip install --upgrade -r /home/site/wwwroot/requirements.txt
    ```          

12. Po nainstalování nových balíčků restartujte službu App Service na webu Azure Portal stisknutím tlačítka **Restartovat**. 

    > [!Tip] 
    > Pokud v souboru `requirements.txt` vaší aplikace provedete nějaké změny, nezapomeňte opět pomocí konzoly Kudu nainstalovat všechny balíčky uvedené v tomto souboru. 

13. Jakmile dokončíte konfiguraci serverového prostředí, aktualizujte stránku v prohlížeči. Měla by se zobrazit webová aplikace.

    ![Výsledky publikování aplikací Bottle, Flask a Django ve službě App Service](./media/sql-api-python-application/python-published-app-services.png)

    > [!Tip] 
    > Pokud se webová stránka nezobrazí nebo se stále zobrazuje zpráva „The page cannot be displayed because an internal server error has occurred“ (Stránku není možné zobrazit, protože došlo k interní chybě serveru), otevřete v Kudo soubor web.config, do části system.webServer přidejte ` <httpErrors errorMode="Detailed"></httpErrors>` a pak stránku aktualizujte. Díky tomu se v prohlížeči zobrazí podrobný chybový výstup. 

## <a name="troubleshooting"></a>Řešení potíží
Pokud je toto první aplikace Python, kterou jste spustili na svém počítači, ujistěte se, že vaše proměnná PATH obsahuje následující složky (nebo ekvivalentní umístění instalací):

    C:\Python27\site-packages;C:\Python27\;C:\Python27\Scripts;

Pokud jste projekt pojmenovali jinak než **tutorial** a na stránce hlasování se zobrazí chyba, ujistěte se, že soubor **\_\_init\_\_.py** odkazuje na následujícím řádku na správný projekt: `import tutorial.view`.

## <a name="next-steps"></a>Další postup
Blahopřejeme! Dokončili jste svou první webovou aplikaci Python, která využívá službu Azure Cosmos DB, a publikovali jste ji v Azure.

Pokud chcete do své webové aplikace přidat další funkce, podívejte se na rozhraní API dostupná v [sadě SDK služby Azure Cosmos DB pro Python](sql-api-sdk-python.md).

Další informace o Azure, nástroji Visual Studio a Pythonu najdete v [Centru pro vývojáře v Pythonu](https://azure.microsoft.com/develop/python/). 

Další kurzy Pythonu Flask najdete na stránce [Velký kurz na Flask, část I: Hello, World!](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world). 

---
title: 'Kurz: spuštění moderního čtečky pomocí Pythonu'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu vytvoříte aplikaci v Pythonu, která spustí moderní čtečku.
services: cognitive-services
author: dylankil
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: dylankil
ms.custom: tracking-python
ms.openlocfilehash: fb95e21f9df5cf5eded03ea51f56bab86bbbecb3
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84609127"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-python-sample-project"></a>Kurz: spuštění moderního čtečky pomocí ukázkového projektu Python

V tomto [přehledu](./overview.md)jste se dozvěděli o tom, co je moderní čtečka a jak implementuje osvědčené techniky pro zlepšení porozumění čtení pro jazykové učení, vznikající čtenáři a studenty s rozdíly v učení. Tento kurz popisuje, jak vytvořit webovou aplikaci v Pythonu, která spustí moderní čtečku. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření webové aplikace v Pythonu pomocí PIP, baňky, Jinja a virtualenv s použitím ukázkového projektu
> * Získání přístupového tokenu
> * Spuštění moderního čtecího zařízení s ukázkovým obsahem

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

## <a name="prerequisites"></a>Požadavky

* Prostředek moderního čtecího zařízení nakonfigurovaný pro ověřování Azure Active Directory. Pomocí [těchto pokynů](./how-to-create-immersive-reader.md) si můžete nastavit. Při konfiguraci vlastností prostředí budete potřebovat některé z hodnot, které jsou zde vytvořeny. Uložte výstup vaší relace do textového souboru pro budoucí referenci.
* [Git](https://git-scm.com/)
* [SADA moderní čtečky](https://github.com/microsoft/immersive-reader-sdk)
* [Python](https://www.python.org/downloads/) a [PIP](https://docs.python.org/3/installing/index.html). Od Pythonu 3,4 je PIP ve výchozím nastavení součástí s binárními instalačními programy Pythonu.
* [Reakční](https://flask.palletsprojects.com/en/1.0.x/)
* [Jinja](http://jinja.pocoo.org/docs/2.10/)
* [virtualenv](https://virtualenv.pypa.io/en/latest/) a [virtualenvwrapper-Win pro Windows](https://pypi.org/project/virtualenvwrapper-win/) nebo [virtualenvwrapper pro OSX](https://virtualenvwrapper.readthedocs.io/en/latest/)
* [požadavky – modul](https://pypi.org/project/requests/2.7.0/)
* Rozhraní IDE, jako je například [Visual Studio Code](https://code.visualstudio.com/)

## <a name="configure-authentication-credentials"></a>Konfigurace ověřovacích přihlašovacích údajů

Vytvořte nový soubor s názvem _. env_a vložte do něj následující kód a zadejte hodnoty, které jste zadali při vytváření prostředku pro moderní čtečku.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Ujistěte se, že tento soubor nechcete potvrdit do správy zdrojových kódů, protože obsahuje tajné klíče, které by neměly být zveřejněny.

Koncový bod rozhraní API **getimmersivereadertoken** by měl být zabezpečený za určitou formou ověřování (například [OAuth](https://oauth.net/2/)), aby se zabránilo neoprávněným uživatelům ve získávání tokenů k použití proti vaší službě s moderní čtečkou a fakturaci. Tato práce překračuje rámec tohoto kurzu.

## <a name="create-a-python-web-app-on-windows"></a>Vytvoření webové aplikace v Pythonu ve Windows

Vytvoření webové aplikace v Pythonu pomocí `flask` systému Windows.

Nainstalujte [Git](https://git-scm.com/).

Po instalaci Git otevřete příkazový řádek a naklonujte úložiště Git pro moderní čtečku do složky ve vašem počítači.

```cmd
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Nainstalujte [Python](https://www.python.org/downloads/).

Zaškrtněte políčko Přidat Python do cesty.

![Dialog Windows pro instalaci Pythonu – krok 1](./media/pythoninstallone.jpg)

Přidejte volitelné funkce zaškrtnutím příslušných políček a potom klikněte na tlačítko Další.

![Dialogové okno instalace systému Windows Python – krok 2](./media/pythoninstalltwo.jpg)

Zvolte vlastní instalace a nastavte cestu instalace jako kořenovou složku, třeba `C:\Python37-32\` pak klikněte na tlačítko nainstalovat.

![Dialog pro instalaci Windows v Pythonu – krok 3](./media/pythoninstallthree.jpg)

Po dokončení instalace Pythonu otevřete příkazový řádek a `cd` složku Python Scripts.

```cmd
cd C:\Python37-32\Scripts
```

Nainstalovat baňce.

```cmd
pip install flask
```

Nainstalujte Jinja2. Plnohodnotný modul šablon pro Python

```cmd
pip install jinja2
```

Nainstalujte virtualenv. Nástroj pro vytváření izolovaných prostředí Pythonu

```cmd
pip install virtualenv
```

Nainstalujte virtualenvwrapper-Win. Nápad za virtualenvwrapper je snadné použití virtualenv.

```cmd
pip install virtualenvwrapper-win
```

Nainstalujte modul požadavky. Požadavky jsou apache2 licencovaná knihovna HTTP, která je napsaná v Pythonu.

```cmd
pip install requests
```

Nainstalujte modul Python-dotenv. Tento modul přečte dvojici klíč-hodnota ze souboru. ENV a přidá je do proměnné prostředí.

```cmd
pip install python-dotenv
```

Vytvoření virtuálního prostředí

```cmd
mkvirtualenv advanced-python
```

`cd`do ukázkové kořenové složky projektu.

```cmd
cd C:\immersive-reader-sdk\js\samples\advanced-python
```

Připojte vzorový projekt k prostředí. Tím se namapuje nově vytvořené virtuální prostředí na vzorovou kořenovou složku projektu.

```cmd
setprojectdir .
```

Aktivujte virtuální prostředí.

```cmd
activate
```

Projekt by teď měl být aktivní a na příkazovém řádku se zobrazí něco podobného `(advanced-python) C:\immersive-reader-sdk\js\samples\advanced-python>` .

Deaktivuje prostředí.

```cmd
deactivate
```

`(advanced-python)`Předpona by nyní měla být pryč, protože prostředí je nyní deaktivováno.

Chcete-li znovu aktivovat prostředí, spusťte `workon advanced-python` z kořenové složky ukázkového projektu.

```cmd
workon advanced-python
```

### <a name="launch-the-immersive-reader-with-sample-content"></a>Spuštění moderního čtecího zařízení s ukázkovým obsahem

Když je prostředí aktivní, spusťte vzorový projekt zadáním `flask run` z kořenové složky ukázkového projektu.

```cmd
flask run
```

Otevřete prohlížeč a přejděte na _http://localhost:5000_ .

## <a name="create-a-python-web-app-on-osx"></a>Vytvoření webové aplikace v Pythonu v OSX

Vytvoření webové aplikace v Pythonu pomocí `flask` OSX.

Nainstalujte [Git](https://git-scm.com/).

Po instalaci Gitu otevřete terminál a naklonujte úložiště Git pro moderní čtečku do složky ve vašem počítači.

```bash
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Nainstalujte [Python](https://www.python.org/downloads/).

Kořenová složka Pythonu `Python37-32` by měla být nyní ve složce aplikace.

Po dokončení instalace Pythonu otevřete terminál a `cd` složku Python Scripts.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Nainstalovat pip.

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

Pak spuštěním následujícího příkazu nainstalujte PIP pro aktuálně přihlášeného uživatele, aby nedocházelo k problémům s oprávněními.

```bash
python get-pip.py --user
```

```bash
sudo nano /etc/paths
```

- Po zobrazení výzvy zadejte heslo.
- Přidejte cestu k instalaci PIP do proměnné PATH.
- Přejděte do dolní části souboru a zadejte cestu, kterou chcete přidat jako poslední položku seznamu, např. `PATH=$PATH:/usr/local/bin` .
- Stisknutím klávesy CTRL-x ukončíte.
- Zadáním `Y` uložte upravenou vyrovnávací paměť.
- A je to! Chcete-li ji otestovat, v okně nové okno terminálu zadejte: `echo $PATH` .

Nainstalovat baňce.

```bash
pip install flask --user
```

Nainstalujte Jinja2. Plnohodnotný modul šablon pro Python

```bash
pip install Jinja2 --user
```

Nainstalujte virtualenv. Nástroj pro vytváření izolovaných prostředí Pythonu

```bash
pip install virtualenv --user
```

Nainstalujte virtualenvwrapper. Nápad za virtualenvwrapper je snadné použití virtualenv.

```bash
pip install virtualenvwrapper --user
```

Nainstalujte modul požadavky. Požadavky jsou apache2 licencovaná knihovna HTTP, která je napsaná v Pythonu.

```bash
pip install requests --user
```

Nainstalujte modul Python-dotenv. Tento modul přečte dvojici klíč-hodnota ze souboru. ENV a přidá je do proměnné prostředí.

```bash
pip install python-dotenv --user
```

Vyberte složku, ve které chcete zachovat vaše virtuální prostředí, a spusťte tento příkaz.

```bash
mkdir ~/.virtualenvs
```

`cd`do složky Ukázky aplikace pro moderní čtečku Python.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Vytvoření virtuálního prostředí

```bash
mkvirtualenv -p /usr/local/bin/python3 advanced-python
```

Připojte vzorový projekt k prostředí. Tím se namapuje nově vytvořené virtuální prostředí na vzorovou kořenovou složku projektu.

```bash
setprojectdir .
```

Aktivujte virtuální prostředí.

```bash
activate
```

Projekt by teď měl být aktivní a na příkazovém řádku se zobrazí něco podobného `(advanced-python) /immersive-reader-sdk/js/samples/advanced-python>` .

Deaktivuje prostředí.

```bash
deactivate
```

`(advanced-python)`Předpona by nyní měla být pryč, protože prostředí je nyní deaktivováno.

Chcete-li znovu aktivovat prostředí, spusťte `workon advanced-python` z kořenové složky ukázkového projektu.

```bash
workon advanced-python
```

## <a name="launch-the-immersive-reader-with-sample-content"></a>Spuštění moderního čtecího zařízení s ukázkovým obsahem

Když je prostředí aktivní, spusťte vzorový projekt zadáním `flask run` z kořenové složky ukázkového projektu.

```bash
flask run
```

Otevřete prohlížeč a přejděte na _http://localhost:5000_ .

## <a name="next-steps"></a>Další kroky

* Prozkoumejte [sadu moderních čtenářů](https://github.com/microsoft/immersive-reader-sdk) a [referenční materiály k sadě pro moderní čtečku](./reference.md)
* Zobrazit ukázky kódu na [GitHubu](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)

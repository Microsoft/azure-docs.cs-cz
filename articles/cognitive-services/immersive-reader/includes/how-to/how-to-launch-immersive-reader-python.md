---
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: f88ca39a378e997bb72300188166192e3383f6f1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102620021"
---
## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
* Prostředek moderního čtecího zařízení nakonfigurovaný pro ověřování Azure Active Directory. Pomocí [těchto pokynů](../../how-to-create-immersive-reader.md) si můžete nastavit.  Při konfiguraci vlastností prostředí budete potřebovat některé z hodnot, které jsou zde vytvořeny. Uložte výstup vaší relace do textového souboru pro budoucí referenci.
* [Git](https://git-scm.com/).
* [Sada moderního čtecího zařízení](https://github.com/microsoft/immersive-reader-sdk).
* [Python](https://www.python.org/downloads/) a [PIP](https://docs.python.org/3/installing/index.html). Od Pythonu 3,4 je PIP ve výchozím nastavení součástí s binárními instalačními programy Pythonu.
* [Baňka](https://flask.palletsprojects.com/en/1.0.x/).
* [Jinja](http://jinja.pocoo.org/docs/2.10/).
* [virtualenv](https://virtualenv.pypa.io/en/latest/) a [virtualenvwrapper-Win pro Windows](https://pypi.org/project/virtualenvwrapper-win/) nebo [virtualenvwrapper pro OSX](https://virtualenvwrapper.readthedocs.io/en/latest/).
* [Modul žádosti](https://pypi.org/project/requests/2.7.0/).
* Rozhraní IDE, jako je například [Visual Studio Code](https://code.visualstudio.com/).

## <a name="configure-authentication-credentials"></a>Konfigurace ověřovacích přihlašovacích údajů

Vytvořte nový soubor s názvem **. env** a vložte do něj následující názvy a hodnoty. Zadejte hodnoty, které jste zadali při vytváření prostředku pro moderní čtečku.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Tento soubor Nepotvrzujte do správy zdrojových kódů, protože obsahuje tajné klíče, které by neměly být zveřejněny.

Zabezpečte koncový bod rozhraní **getimmersivereadertoken** API za nějakým formulářem ověřování, například [OAuth](https://oauth.net/2/). Ověřování zabrání neautorizovaným uživatelům získat tokeny, které se mají použít, na službu pro moderní čtečku a pro účely fakturace. Tato práce překračuje rámec tohoto kurzu.

## <a name="create-a-python-web-app-on-windows"></a>Vytvoření webové aplikace v Pythonu ve Windows

Vytvoření webové aplikace v Pythonu pomocí `flask` systému Windows.

Nainstalujte [Git](https://git-scm.com/).

Po instalaci Git otevřete příkazový řádek a naklonujte úložiště Git sady pro moderní čtečku do složky ve vašem počítači.

```cmd
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Nainstalujte [Python](https://www.python.org/downloads/).

Zaškrtněte políčko **Přidat Python do cesty** .

![Dialog Windows pro instalaci Pythonu – krok 1](../../media/pythoninstallone.jpg)

Přidejte **volitelné funkce** zaškrtnutím políček a pak vyberte **Další**.

![Dialogové okno instalace systému Windows Python – krok 2](../../media/pythoninstalltwo.jpg)

Vyberte možnost **vlastní instalace** a nastavte cestu instalace jako kořenovou složku, například `C:\Python37-32\` . Pak vyberte **nainstalovat**.

![Dialog pro instalaci Windows v Pythonu – krok 3](../../media/pythoninstallthree.jpg)

Po dokončení instalace Pythonu otevřete příkazový řádek a použijte příkaz `cd` pro přechod do složky Python Scripts.

```cmd
cd C:\Python37-32\Scripts
```

Nainstalovat baňce.

```cmd
pip install flask
```

Nainstalujte Jinja2. Je to plnohodnotný modul šablon pro Python.

```cmd
pip install jinja2
```

Nainstalujte virtualenv. Tento nástroj vytváří izolovaná prostředí Pythonu.

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

Vytvořte virtuální prostředí.

```cmd
mkvirtualenv advanced-python
```

Použijte `cd` pro přechod na kořenovou složku ukázkového projektu.

```cmd
cd C:\immersive-reader-sdk\js\samples\advanced-python
```

Připojte vzorový projekt k prostředí. Tato akce provede mapování nově vytvořeného virtuálního prostředí na kořenovou složku projektu s ukázkovou příponou.

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

`(advanced-python)`Předpona by měla být pryč, protože prostředí je deaktivováno.

Chcete-li znovu aktivovat prostředí, spusťte `workon advanced-python` z kořenové složky ukázkového projektu.

```cmd
workon advanced-python
```

### <a name="start-the-immersive-reader-with-sample-content"></a>Spuštění moderního čtecího zařízení s ukázkovým obsahem

Když je prostředí aktivní, spusťte vzorový projekt zadáním `flask run` z kořenové složky ukázkového projektu.

```cmd
flask run
```

Otevřete prohlížeč a pokračujte na http://localhost:5000 .

## <a name="create-a-python-web-app-on-osx"></a>Vytvoření webové aplikace v Pythonu v OSX

Vytvořte webovou aplikaci v Pythonu pomocí `flask` OSX.

Nainstalujte [Git](https://git-scm.com/).

Po instalaci Git otevřete terminál a naklonujte úložiště Git sady pro moderní čtečku do složky ve vašem počítači.

```bash
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Nainstalujte [Python](https://www.python.org/downloads/).

Kořenová složka Pythonu, například `Python37-32` , by měla být nyní ve složce aplikace.

Po dokončení instalace Pythonu otevřete terminál a použijte k tomu, `cd` abyste přešli do složky Python Scripts.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Nainstalovat pip.

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

Spusťte následující kód, který nainstaluje PIP pro aktuálně přihlášeného uživatele, aby nedocházelo k problémům s oprávněními.

```bash
python get-pip.py --user
```

```bash
sudo nano /etc/paths
```

- Po zobrazení výzvy zadejte heslo.
- Přidejte cestu k instalaci PIP do proměnné PATH.
- Přejděte do dolní části souboru a zadejte cestu, kterou chcete přidat jako poslední položku seznamu, například `PATH=$PATH:/usr/local/bin` .
- Pro ukončení vyberte **CTRL + X** .
- Zadáním **Y** uložte upravenou vyrovnávací paměť.
- A to je vše! Chcete-li ji otestovat, zadejte do nového okna terminálu `echo $PATH` .

Nainstalovat baňce.

```bash
pip install flask --user
```

Nainstalujte Jinja2. Je to plnohodnotný modul šablon pro Python.

```bash
pip install Jinja2 --user
```

Nainstalujte virtualenv. Tento nástroj vytváří izolovaná prostředí Pythonu.

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

Vyberte složku, ve které chcete zachovat vaše virtuální prostředí, a spusťte tento příkaz:

```bash
mkdir ~/.virtualenvs
```

Použijte `cd` k tomu, abyste přešli do složky pro ukázkovou aplikaci moderního čtecího zařízení Python sady SDK.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Vytvořte virtuální prostředí.

```bash
mkvirtualenv -p /usr/local/bin/python3 advanced-python
```

Připojte vzorový projekt k prostředí. Tato akce provede mapování nově vytvořeného virtuálního prostředí na kořenovou složku projektu s ukázkovou příponou.

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

`(advanced-python)`Předpona by měla být pryč, protože prostředí je deaktivováno.

Chcete-li znovu aktivovat prostředí, spusťte `workon advanced-python` z kořenové složky ukázkového projektu.

```bash
workon advanced-python
```

## <a name="start-the-immersive-reader-with-sample-content"></a>Spuštění moderního čtecího zařízení s ukázkovým obsahem

Když je prostředí aktivní, spusťte vzorový projekt zadáním `flask run` z kořenové složky ukázkového projektu.

```bash
flask run
```

Otevřete prohlížeč a pokračujte na http://localhost:5000 .

## <a name="next-steps"></a>Další kroky

* Prozkoumejte [sadu moderních čtenářů](https://github.com/microsoft/immersive-reader-sdk) a [referenční materiály k sadě pro moderní čtečku](../../reference.md).
* Zobrazení ukázek kódu na [GitHubu](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/).

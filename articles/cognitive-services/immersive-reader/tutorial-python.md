---
title: 'Kurz: Spuštění immersive Reader pomocí Pythonu'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu vytvoříte aplikaci Pythonu, která spustí immersive Reader.
services: cognitive-services
author: dylankil
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: dylankil
ms.openlocfilehash: a252afae0a007ee0b791b56d19ffb0685848d30a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844356"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-python-sample-project"></a>Kurz: Spuštění immersive Reader pomocí ukázkového projektu Pythonu

V [přehledu](./overview.md)jste se dozvěděli o tom, co je Immersive Reader a jak implementuje osvědčené techniky ke zlepšení porozumění čtení pro studenty jazyků, začínající čtenáře a studenty s rozdíly v učení. Tento kurz popisuje, jak vytvořit webovou aplikaci Pythonu, která spustí immersive Reader. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření webové aplikace Pythonu s Pipem, Flaskem, Jinjou a virtualenv pomocí ukázkového projektu
> * Získání přístupového tokenu
> * Spuštění čtečky Immersive Reader s ukázkovým obsahem

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

* Prostředek immersive Reader nakonfigurovaný pro ověřování Azure Active Directory. Podle [těchto pokynů](./how-to-create-immersive-reader.md) se připravte. Při konfiguraci vlastností prostředí budete potřebovat některé hodnoty vytvořené zde. Uložte výstup relace do textového souboru pro budoucí použití.
* [Git](https://git-scm.com/)
* [Sada SDK pro pohlcující čtečku](https://github.com/microsoft/immersive-reader-sdk)
* [Python](https://www.python.org/downloads/) a [pip](https://docs.python.org/3/installing/index.html). Počínaje Pythonem 3.4 je pip standardně součástí binárních instalačních programů Pythonu.
* [Baňky](https://flask.palletsprojects.com/en/1.0.x/)
* [Jinja](http://jinja.pocoo.org/docs/2.10/)
* [virtualenv](https://virtualenv.pypa.io/en/latest/) a [virtualenvwrapper-win pro Windows](https://pypi.org/project/virtualenvwrapper-win/) nebo [virtualenvwrapper pro OSX](https://virtualenvwrapper.readthedocs.io/en/latest/)
* [modul požadavků](https://pypi.org/project/requests/2.7.0/)
* IDE, jako je [například visual studio kód](https://code.visualstudio.com/)

## <a name="configure-authentication-credentials"></a>Konfigurace ověřovacích pověření

Vytvořte nový soubor s názvem _ENV_a vložte do něj následující kód, který zadá hodnoty dané při vytváření prostředku aplikace Immersive Reader.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Ujistěte se, že tento soubor není potvrzena do správy zdrojového kódu, protože obsahuje tajné klíče, které by neměly být zveřejněny.

Koncový bod rozhraní API **getimmersivereadertoken** by měl být zabezpečen za nějakou formu ověřování (například [OAuth),](https://oauth.net/2/)aby se zabránilo neoprávněným uživatelům získat tokeny pro použití proti vaší službě Immersive Reader a fakturace; že práce je nad rámec tohoto kurzu.

## <a name="create-a-python-web-app-on-windows"></a>Vytvoření webové aplikace Pythonu ve Windows

Vytvořte webovou `flask` aplikaci Pythonu pomocí Windows.

Nainstalujte [Git](https://git-scm.com/).

Po instalaci Gitu otevřete příkazový řádek a "klonujte" úložiště Immersive Reader SDK Git do složky v počítači

```cmd
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Nainstalujte [Python](https://www.python.org/downloads/).

Zaškrtněte políčko Přidat Python do CESTY.

![Krok 1 instalačního programu v Pythonu pro Windows](./media/pythoninstallone.jpg)

Přidejte volitelné funkce zaškrtnutím políček a kliknutím na tlačítko Další.

![Krok 2 instalačního programu v Pythonu pro Windows](./media/pythoninstalltwo.jpg)

Zvolte "Vlastní instalace" a nastavte instalační cestu jako `C:\Python37-32\` kořenovou složku, například klikněte na tlačítko "Instalovat".

![Krok instalace v Pythonu pro Windows 3](./media/pythoninstallthree.jpg)

Po dokončení instalace Pythonu otevřete `cd` příkazový řádek a složku Skripty Pythonu.

```cmd
cd C:\Python37-32\Scripts
```

Nainstalujte baňku.

```cmd
pip install flask
```

Nainstalujte Jinja2. Plně vybavený modul šablon pro Python.

```cmd
pip install jinja2
```

Nainstalujte virtualenv. Nástroj pro vytváření izolovaných prostředí Pythonu.

```cmd
pip install virtualenv
```

Nainstalujte virtualenvwrapper-win. Myšlenka virtualenvwrapper je usnadnit používání virtualenv.

```cmd
pip install virtualenvwrapper-win
```

Nainstalujte modul požadavků. Požadavky je Apache2 licencovaná HTTP knihovna, napsaná v Pythonu.

```cmd
pip install requests
```

Nainstalujte modul python-dotenv. Tento modul přečte dvojici klíč-hodnota ze souboru .env a přidá je do proměnné prostředí.

```cmd
pip install python-dotenv
```

Vytvoření virtuálního prostředí

```cmd
mkvirtualenv advanced-python
```

`cd`do kořenové složky ukázkového projektu.

```cmd
cd C:\immersive-reader-sdk\js\samples\advanced-python
```

Připojte ukázkový projekt k prostředí. To mapuje nově vytvořené virtuální prostředí do kořenové složky ukázkového projektu.

```cmd
setprojectdir .
```

Aktivujte virtuální prostředí.

```cmd
activate
```

Projekt by nyní měl být aktivní a `(advanced-python) C:\immersive-reader-sdk\js\samples\advanced-python>` v příkazovém řádku se zobrazí něco podobného.

Deaktivujte prostředí.

```cmd
deactivate
```

Předpona `(advanced-python)` by nyní měla být pryč, protože prostředí je nyní deaktivováno.

Chcete-li znovu `workon advanced-python` aktivovat prostředí spustit z kořenové složky ukázkového projektu.

```cmd
workon advanced-python
```

### <a name="launch-the-immersive-reader-with-sample-content"></a>Spuštění čtečky Immersive Reader s ukázkovým obsahem

Když je prostředí aktivní, spusťte `flask run` ukázkový projekt zadáním z kořenové složky ukázkového projektu.

```cmd
flask run
```

Otevřete prohlížeč a _http://localhost:5000_přejděte na .

## <a name="create-a-python-web-app-on-osx"></a>Vytvoření webové aplikace pythonu v OSX

Vytvořte webovou `flask` aplikaci Pythonu pomocí OSX.

Nainstalujte [Git](https://git-scm.com/).

Po instalaci Gitu otevřete terminál a "klonujte" úložiště Immersive Reader SDK Git do složky v počítači

```bash
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Nainstalujte [Python](https://www.python.org/downloads/).

Kořenová složka Pythonu, `Python37-32` například, by nyní měla být ve složce Aplikace.

Po dokončení instalace Pythonu `cd` otevřete terminál a složku Skripty Pythonu.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Nainstalovat pip.

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

Potom spusťte následující a nainstalujte pip pro aktuálně přihlášeného uživatele, abyste se vyhnuli problémům s oprávněními.

```bash
python get-pip.py --user
```

```bash
sudo nano /etc/paths
```

- Po zobrazení výzvy zadejte heslo.
- Přidejte cestu k instalaci pipu do proměnné PATH.
- Přejděte na konec souboru a zadejte cestu, kterou chcete přidat jako poslední `PATH=$PATH:/usr/local/bin`položku seznamu, například .
- Hit control-x přestat.
- Zadáním `Y` zadejte uložení upravené vyrovnávací paměti.
- A to je vše! Chcete-li jej otestovat, zadejte do nového okna terminálu: `echo $PATH`.

Nainstalujte baňku.

```bash
pip install flask --user
```

Nainstalujte Jinja2. Plně vybavený modul šablon pro Python.

```bash
pip install Jinja2 --user
```

Nainstalujte virtualenv. Nástroj pro vytváření izolovaných prostředí Pythonu.

```bash
pip install virtualenv --user
```

Nainstalujte virtualenvwrapper. Myšlenka virtualenvwrapper je usnadnit používání virtualenv.

```bash
pip install virtualenvwrapper --user
```

Nainstalujte modul požadavků. Požadavky je Apache2 licencovaná HTTP knihovna, napsaná v Pythonu.

```bash
pip install requests --user
```

Nainstalujte modul python-dotenv. Tento modul přečte dvojici klíč-hodnota ze souboru .env a přidá je do proměnné prostředí.

```bash
pip install python-dotenv --user
```

Vyberte složku, ve které chcete zachovat virtuální prostředí, a spusťte tento příkaz.

```bash
mkdir ~/.virtualenvs
```

`cd`do ukázkové složky aplikace Immersive Reader SDK Python.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Vytvoření virtuálního prostředí

```bash
mkvirtualenv -p /usr/local/bin/python3 advanced-python
```

Připojte ukázkový projekt k prostředí. To mapuje nově vytvořené virtuální prostředí do kořenové složky ukázkového projektu.

```bash
setprojectdir .
```

Aktivujte virtuální prostředí.

```bash
activate
```

Projekt by nyní měl být aktivní a `(advanced-python) /immersive-reader-sdk/js/samples/advanced-python>` v příkazovém řádku se zobrazí něco podobného.

Deaktivujte prostředí.

```bash
deactivate
```

Předpona `(advanced-python)` by nyní měla být pryč, protože prostředí je nyní deaktivováno.

Chcete-li znovu `workon advanced-python` aktivovat prostředí spustit z kořenové složky ukázkového projektu.

```bash
workon advanced-python
```

## <a name="launch-the-immersive-reader-with-sample-content"></a>Spuštění čtečky Immersive Reader s ukázkovým obsahem

Když je prostředí aktivní, spusťte `flask run` ukázkový projekt zadáním z kořenové složky ukázkového projektu.

```bash
flask run
```

Otevřete prohlížeč a _http://localhost:5000_přejděte na .

## <a name="next-steps"></a>Další kroky

* Seznamte se s [sadou Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) a [referenční sadou Immersive Reader SDK](./reference.md)
* Zobrazení ukázek kódu na [GitHubu](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)

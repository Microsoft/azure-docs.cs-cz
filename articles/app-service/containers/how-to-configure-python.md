---
title: Konfigurace integrované image Pythonu ve službě Azure App Service
description: Tento kurz popisuje možnosti pro vytváření a konfiguraci pythonové aplikace ve službě Azure App Service pomocí integrované image Pythonu.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/25/2018
ms.author: astay;cephalin
ms.custom: mvc
ms.openlocfilehash: 9316805993b81e4d2511e833e0cc8f240807a1f9
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228544"
---
# <a name="configure-built-in-python-image-in-azure-app-service-preview"></a>Konfigurace integrované image Pythonu ve službě Azure App Service (Preview)

V tomto článku se dozvíte, jak nakonfigurovat integrovanou image Pythonu ve službě [App Service v Linuxu](app-service-linux-intro.md) tak, abyste mohli spouštět své pythonové aplikace.

## <a name="python-version"></a>Verze Pythonu

Modul runtime Pythonu ve službě App Service v Linuxu používá verzi `python-3.7.0`.

## <a name="supported-frameworks"></a>Podporované architektury

Podporují se všechny verze webových architektur, které dodržují rozhraní WSGI (Web Server Gateway Interface) a jsou kompatibilní s modulem runtime `python-3.7`.

## <a name="package-management"></a>Správa balíčků

Během publikování na Git modul Kudu hledá v kořenu úložiště soubor [requirements.txt](https://pip.pypa.io/en/stable/user_guide/#requirements-files) a automaticky instaluje balíčky v Azure pomocí `pip`.

Pokud chcete tento soubor vygenerovat před samotnou publikací, přejděte do kořenu úložiště a spusťte v pythonovém prostředí tento příkaz:

```bash
pip freeze > requirements.txt
```

## <a name="configure-your-python-app"></a>Konfigurace pythonové aplikace

Integrovaná image Pythonu ve službě App Service používá server [Gunicorn](http://gunicorn.org/), pomocí kterého spouští pythonové aplikace. Gunicorn je pythonový server WSGI HTTP pro Unix. Pro projekty Django a Flask služba App Service konfiguruje Gunicorn automaticky.

### <a name="django-app"></a>Aplikace Django

Když publikujete projekt Django, který obsahuje modul `wsgi.py`, Azure automaticky zavolá Gunicorn pomocí tohoto příkazu:

```bash
gunicorn <path_to_wsgi>
```

### <a name="flask-app"></a>Aplikace Flask

Když publikujete aplikaci Flask a vstupní bod se nachází v modulu `application.py` nebo `app.py`, Azure automaticky zavolá Gunicorn pomocí jednoho z těchto příkazů:

```bash
gunicorn application:app
```

Nebo 

```bash
gunicorn app:app
```

### <a name="customize-start-up"></a>Přizpůsobení spuštění

Pokud chcete pro svou aplikaci definovat vlastní vstupní bod, nejdříve vytvořte soubor _.txt_ s vlastním příkazem Gunicorn a umístěte ho do kořenu projektu. Pokud chcete například spustit server s modulem _helloworld.py_ a proměnnou `app`, vytvořte _startup.txt_ s tímto obsahem:

```bash
gunicorn helloworld:app
```

Na stránce [Nastavení aplikace](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) vyberte jako **Zásobník modulu runtime** **Python|3.7** a zadejte název **Spouštěcího souboru** z předchozího kroku. Třeba _startup.txt_.

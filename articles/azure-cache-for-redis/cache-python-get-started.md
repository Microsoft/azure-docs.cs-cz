---
title: 'Rychlý Start: Vytvoření aplikace v Pythonu, která používá Azure cache pro Redis'
description: V tomto rychlém startu se dozvíte, jak vytvořit aplikaci v Pythonu, která používá Azure cache pro Redis.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: v-lincan
ms.assetid: f186202c-fdad-4398-af8c-aee91ec96ba3
ms.service: cache
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 05/11/2018
ms.author: yegu
ms.custom:
- mvc
- seo-python-october2019
ms.openlocfilehash: 87c22d3497765fca6f0dcae445152e6e2923510e
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329871"
---
# <a name="quickstart-use-azure-cache-for-redis-with-python"></a>Rychlý Start: použití mezipaměti Azure pro Redis s Pythonem

V tomto článku zahrňte Azure cache pro Redis do aplikace v Pythonu, abyste měli přístup k zabezpečené vyhrazené mezipaměti, která je přístupná z libovolné aplikace v Azure.

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
- [Python 2 nebo 3](https://www.python.org/downloads/)

## <a name="create-an-azure-cache-for-redis-on-azure"></a>Vytvoření mezipaměti Azure pro Redis v Azure
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="install-redis-py"></a>Instalace redis-py

[Redis-py](https://github.com/andymccurdy/redis-py) je rozhraní Pythonu pro službu Azure cache pro Redis. Pomocí nástroje pro správu balíčků Pythonu *pip* nainstalujte balíček redis-py. 

Následující příklad používá *PIP3* pro python3 k instalaci balíčku Redis-py na Windows 10 pomocí příkazového řádku pro vývojáře sady Visual Studio 2019, který běží se zvýšenými oprávněními správce.

```python
    pip3 install redis
```

![Instalace redis-py](./media/cache-python-get-started/cache-python-install-redis-py.png)


## <a name="read-and-write-to-the-cache"></a>Čtení z mezipaměti a zápis do mezipaměti

Spusťte Python a otestujte používání mezipaměti z příkazového řádku. Nahraďte `<Your Host Name>` a `<Your Access Key>` hodnotami pro mezipaměť Azure pro Redis. 

```python
>>> import redis
>>> r = redis.StrictRedis(host='<Your Host Name>.redis.cache.windows.net',
        port=6380, db=0, password='<Your Access Key>', ssl=True)
>>> r.set('foo', 'bar')
True
>>> r.get('foo')
b'bar'
```

> [!IMPORTANT]
> Pro Redis verze je 3,0 nebo vyšší, vynutila se ověření certifikátu SSL. ssl_ca_certs musí být explicitně nastavené při připojování k Redis. V případě systému RH Linux se ssl_ca_certs dá najít v modulu certifikátů "/etc/pki/tls/certs/ca-bundle.CRT".

## <a name="create-a-python-script"></a>Vytvoření skriptu Pythonu

Vytvořte nový textový soubor skriptu *PythonApplication1.py*.

Do souboru *PythonApplication1.py* přidejte následující skript a uložte ho. Tento skript otestuje přístup k mezipaměti. Nahraďte `<Your Host Name>` a `<Your Access Key>` hodnotami pro mezipaměť Azure pro Redis. 

```python
import redis

myHostname = "<Your Host Name>.redis.cache.windows.net"
myPassword = "<Your Access Key>"

r = redis.StrictRedis(host=myHostname, port=6380,
                      password=myPassword, ssl=True)

result = r.ping()
print("Ping returned : " + str(result))

result = r.set("Message", "Hello!, The cache is working with Python!")
print("SET Message returned : " + str(result))

result = r.get("Message")
print("GET Message returned : " + result.decode("utf-8"))

result = r.client_list()
print("CLIENT LIST returned : ")
for c in result:
    print("id : " + c['id'] + ", addr : " + c['addr'])
```

Spusťte skript s Pythonem.

![Dokončení testu Pythonu](./media/cache-python-get-started/cache-python-completed.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud budete pokračovat dalším kurzem, můžete prostředky vytvořené v tomto rychlém startu zachovat a znovu je použít.

V opačném případě, pokud jste už s ukázkovou aplikací v tomto rychlém startu skončili, můžete prostředky Azure vytvořené v tomto rychlém startu odstranit, abyste se vyhnuli poplatkům. 

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné a skupina prostředků včetně všech v ní obsažených prostředků bude trvale odstraněna. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. Pokud jste vytvořili prostředky pro hostování této ukázky ve stávající skupině prostředků obsahující prostředky, které chcete zachovat, můžete místo odstranění skupiny prostředků odstranit jednotlivé prostředky z jejich odpovídajících oken.
>

Přihlaste se k webu [Azure Portal](https://portal.azure.com) a potom vyberte **Skupiny prostředků**.

Do textového pole **filtrovat podle názvu...** zadejte název vaší skupiny prostředků. V pokynech v tomto článku se používala skupina prostředků *TestResources*. Ve vaší skupině prostředků v seznamu výsledků vyberte **...** a pak **odstraňte skupinu prostředků**.

![Odstranit](./media/cache-web-app-howto/cache-delete-resource-group.png)

Zobrazí se výzva k potvrzení odstranění skupiny prostředků. Zadejte název vaší skupiny prostředků, který chcete potvrdit, a vyberte **Odstranit**.

Po chvíli bude skupina prostředků včetně všech obsažených prostředků odstraněná.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvořte jednoduchou webovou aplikaci v ASP.NET, která používá Azure cache pro Redis.](./cache-web-app-howto.md)

<!--Image references-->
[1]: ./media/cache-python-get-started/redis-cache-new-cache-menu.png
[2]: ./media/cache-python-get-started/redis-cache-cache-create.png

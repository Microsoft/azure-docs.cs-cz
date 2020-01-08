---
title: 'Rychlý Start: Vytvoření aplikace v Pythonu – mezipaměť Azure pro Redis'
description: V tomto rychlém startu se dozvíte, jak vytvořit aplikaci v Pythonu, která používá Azure cache pro Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: python
ms.topic: quickstart
ms.custom:
- mvc
- seo-python-october2019
ms.date: 11/05/2019
ms.openlocfilehash: 7b05f12ad3fd3a0f56605d708bbbf06df7e341ed
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433472"
---
# <a name="quickstart-create-a-python-app-that-uses-azure-cache-for-redis"></a>Rychlý Start: Vytvoření aplikace v Pythonu, která používá Azure cache pro Redis

V tomto článku zahrňte Azure cache pro Redis do aplikace v Pythonu, abyste měli přístup k zabezpečené vyhrazené mezipaměti, která je přístupná z libovolné aplikace v Azure.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
- [Python 2 nebo 3](https://www.python.org/downloads/)

## <a name="create-an-azure-cache-for-redis-instance"></a>Vytvoření mezipaměti Azure pro instanci Redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="install-redis-py"></a>Instalace redis-py

[Redis-py](https://github.com/andymccurdy/redis-py) je rozhraní Pythonu pro službu Azure cache pro Redis. K instalaci balíčku *Redis-py* z příkazového řádku použijte nástroj pro balíčky Pythonu ( *PIP*). 

Následující příklad používá *PIP3* pro Python 3 k instalaci *Redis-py* ve Windows 10 z příkazového řádku správce.

![Nainstalujte rozhraní Redis-py Python do Azure cache pro Redis.](./media/cache-python-get-started/cache-python-install-redis-py.png)

## <a name="read-and-write-to-the-cache"></a>Čtení z mezipaměti a zápis do mezipaměti

Spusťte Python z příkazového řádku a otestujte mezipaměť pomocí následujícího kódu. Nahraďte `<Your Host Name>` a `<Your Access Key>` hodnotami z vaší instance Azure cache for Redis. Název hostitele má tvar *\<název DNS >. Redis. cache. Windows. NET*.

```python
>>> import redis
>>> r = redis.StrictRedis(host='<Your Host Name>',
        port=6380, db=0, password='<Your Access Key>', ssl=True)
>>> r.set('foo', 'bar')
True
>>> r.get('foo')
b'bar'
```

> [!IMPORTANT]
> V případě Azure cache pro Redis verze 3,0 nebo vyšší se vynutila ověření certifikátu SSL. ssl_ca_certs musí být explicitně nastavené při připojování k Azure cache pro Redis. V případě systému RedHat Linux jsou ssl_ca_certs v modulu certifikátu */etc/pki/tls/certs/ca-bundle.CRT* .

## <a name="create-a-python-sample-app"></a>Vytvoření ukázkové aplikace v Pythonu

Vytvořte nový textový soubor, přidejte následující skript a uložte ho jako *PythonApplication1.py*. Nahraďte `<Your Host Name>` a `<Your Access Key>` hodnotami z vaší instance Azure cache for Redis. Název hostitele má tvar *\<název DNS >. Redis. cache. Windows. NET*.

```python
import redis

myHostname = "<Your Host Name>"
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

Spusťte *PythonApplication1.py* pomocí Pythonu. Mělo by se zobrazit výsledek podobný následujícímu příkladu:

![Spustit skript Pythonu pro otestování přístupu k mezipaměti](./media/cache-python-get-started/cache-python-completed.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste hotovi s prostředky a skupinami prostředků Azure, které jste vytvořili v tomto rychlém startu, můžete je odstranit, abyste se vyhnuli poplatkům.

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné a skupina prostředků a všechny prostředky v ní se trvale odstraní. Pokud jste instanci Azure cache for Redis vytvořili ve stávající skupině prostředků, kterou chcete zachovat, můžete odstranit jenom mezipaměť tak, že na stránce s **přehledem** mezipaměti vyberete **Odstranit** . 

Odstranění skupiny prostředků a její Redis Cache pro instanci Azure:

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte **skupiny prostředků**.
1. Do textového pole **filtrovat podle názvu** zadejte název skupiny prostředků, která obsahuje vaši instanci mezipaměti, a pak ji vyberte z výsledků hledání. 
1. Na stránce skupiny prostředků vyberte **Odstranit skupinu prostředků**.
1. Zadejte název skupiny prostředků a pak vyberte **Odstranit**.
   
   ![Odstranění skupiny prostředků pro Azure cache pro Redis](./media/cache-python-get-started/delete-your-resource-group-for-azure-cache-for-redis.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvořte jednoduchou webovou aplikaci v ASP.NET, která používá Azure cache pro Redis.](./cache-web-app-howto.md)


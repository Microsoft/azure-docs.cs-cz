---
title: 'Úvodní příručka: Vytvoření aplikace pythonu – Azure Cache for Redis'
description: V tomto rychlém startu se dozvíte, jak vytvořit aplikaci Pythonu, která používá Azure Cache pro Redis.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75433472"
---
# <a name="quickstart-create-a-python-app-that-uses-azure-cache-for-redis"></a>Úvodní příručka: Vytvoření aplikace Pythonu, která používá Azure Cache pro Redis

V tomto článku začlenit Azure Cache pro Redis do aplikace Python u dálné, abyste měli přístup k zabezpečené, vyhrazené mezipaměti, která je přístupná z libovolné aplikace v rámci Azure.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
- [Python 2 nebo 3](https://www.python.org/downloads/)

## <a name="create-an-azure-cache-for-redis-instance"></a>Vytvoření instance Azure Cache for Redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="install-redis-py"></a>Instalace redis-py

[Redis-py](https://github.com/andymccurdy/redis-py) je rozhraní Pythonu do Azure Cache pro Redis. Pomocí nástroje balíčky Pythonu *pip*nainstalujte balíček *redis-py* z příkazového řádku. 

Následující příklad použil *pip3* pro Python 3 k instalaci *redis-py* v systému Windows 10 z příkazového řádku Správce.

![Instalace rozhraní Pythonu redis-py do mezipaměti Azure pro Redis](./media/cache-python-get-started/cache-python-install-redis-py.png)

## <a name="read-and-write-to-the-cache"></a>Čtení z mezipaměti a zápis do mezipaměti

Spusťte Python z příkazového řádku a otestujte mezipaměť pomocí následujícího kódu. Nahraďte `<Your Host Name>` a `<Your Access Key>` s hodnotami z vaší azure cache pro redis instance. Název hostitele má název DNS ve tvaru * \<>.redis.cache.windows.net*.

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
> Pro Azure Cache pro Redis verze 3.0 nebo vyšší, SSL kontrola certifikátu je vynuceno. ssl_ca_certs musí být explicitně nastavena při připojování k Azure Cache pro Redis. Pro RedHat Linux jsou ssl_ca_certs v modulu certifikátu */etc/pki/tls/certs/ca-bundle.crt.*

## <a name="create-a-python-sample-app"></a>Vytvoření ukázkové aplikace v Pythonu

Vytvořte nový textový soubor, přidejte následující skript a uložte jej jako *PythonApplication1.py*. Nahraďte `<Your Host Name>` a `<Your Access Key>` s hodnotami z vaší azure cache pro redis instance. Název hostitele má název DNS ve tvaru * \<>.redis.cache.windows.net*.

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

Spusťte *PythonApplication1.py* s Pythonem. Výsledky byste měli vidět jako v následujícím příkladu:

![Spuštění skriptu Pythonu pro testování přístupu ke mezipaměti](./media/cache-python-get-started/cache-python-completed.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste hotovi se skupinou prostředků Azure a prostředky, které jste vytvořili v tomto rychlém startu, můžete je odstranit, abyste se vyhnuli poplatkům.

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné a skupina prostředků a všechny prostředky v ní jsou trvale odstraněny. Pokud jste vytvořili instanci Azure Cache for Redis v existující skupině prostředků, kterou chcete zachovat, můžete odstranit jenom mezipaměť výběrem **možnosti Odstranit** ze stránky **Přehled** mezipaměti. 

Odstranění skupiny prostředků a její instance Redis Cache pro Azure:

1. Na [webu Azure Portal](https://portal.azure.com)vyhledejte a vyberte **skupiny prostředků**.
1. Do textového pole **Filtr podle názvu** zadejte název skupiny prostředků, která obsahuje instanci mezipaměti, a pak ji vyberte z výsledků hledání. 
1. Na stránce skupiny prostředků vyberte **Odstranit skupinu prostředků**.
1. Zadejte název skupiny prostředků a vyberte **odstranit**.
   
   ![Odstranění skupiny prostředků pro Azure Cache for Redis](./media/cache-python-get-started/delete-your-resource-group-for-azure-cache-for-redis.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvořte jednoduchou ASP.NET webovou aplikaci, která používá Azure Cache for Redis.](./cache-web-app-howto.md)


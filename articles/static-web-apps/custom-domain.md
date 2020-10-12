---
title: Nastavení vlastní domény ve statickém Web Apps Azure
description: Naučte se namapovat vlastní doménu na statickou Web Apps Azure.
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: buhollan
ms.openlocfilehash: 61ec96a35fac6a033fe6c8b65cff156ba63e5e58
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87563342"
---
# <a name="setup-a-custom-domain-in-azure-static-web-apps-preview"></a>Nastavení vlastní domény ve službě Azure Static Web Apps ve verzi Preview

Ve výchozím nastavení poskytuje Azure static Web Apps automaticky generovaný název domény. V tomto článku se dozvíte, jak namapovat vlastní název domény na statickou Web Apps aplikaci Azure.

## <a name="prerequisites"></a>Požadavky

- Název zakoupené domény
- Přístup k vlastnostem konfigurace DNS pro vaši doménu

Při konfiguraci názvů domén se k mapování kořenových domén (například `example.com` ) na IP adresu používají záznamy A. Kořenové domény musí být namapovány přímo na IP adresu, protože specifikace DNS nepovoluje mapování jedné domény do druhé.

## <a name="dns-configuration-options"></a>Možnosti konfigurace DNS

Pro aplikaci je k dispozici několik různých typů konfigurací služby DNS.

| Pokud chcete                                 | Pak...                                                |
| -----------------------------------------------| --------------------------------------------------- |
| Podpora `www.example.com` nebo `blog.example.net`| [Mapování záznamu CNAME](#map-a-cname-record)           |
| Pracovníky `example.com`                          | [Konfigurace kořenové domény](#configure-a-root-domain) |
| Nasměrovat všechny subdomény na `www.example.com`      | [Mapování zástupného znaku](#map-a-wildcard-domain)            |

## <a name="map-a-cname-record"></a>Mapování záznamu CNAME

Záznam CNAME mapuje jednu doménu do druhé. Záznam CNAME můžete použít k mapování `www.example.com` , `blog.example.com` nebo jakékoli jiné subdomény na automaticky generovanou doménu, kterou poskytuje služba Azure static Web Apps.

1. Otevřete [Azure Portal](https://portal.azure.com) a přihlaste se pomocí svého účtu Azure.

1. Vyhledat a vybrat **statické Web Apps**

1. Na stránce _statický Web Apps_ vyberte název vaší aplikace.

1. V nabídce klikněte na **vlastní domény** .

1. V okně _vlastní domény_ zkopírujte adresu URL do pole **hodnota** .

### <a name="configure-dns-provider"></a>Konfigurace poskytovatele DNS

1. Přihlaste se k webu vašeho poskytovatele domény.

2. Vyhledejte stránku pro správu záznamů DNS. Každý poskytovatel domén má vlastní rozhraní pro správu záznamů DNS, takže se obraťte na dokumentaci poskytovatele. Hledejte oblasti webu označené jako **Domain Name** (Název domény), **DNS** nebo **Name Server Management** (Správa názvových serverů).

3. Často můžete stránku záznamů DNS najít tak, že zobrazíte informace o vašem účtu a vyhledáte odkaz jako **My domains** (Moje domény). Přejít na tuto stránku a vyhledat odkaz s názvem podobný **souboru zóny**, **záznamům DNS**nebo **rozšířené konfiguraci**.

    Následující snímek obrazovky obsahuje příklad stránky záznamů DNS:

    :::image type="content" source="media/custom-domain/example-record-ui.png" alt-text="Ukázka konfigurace poskytovatele DNS":::

4. Vytvoří nový záznam **CNAME** s následujícími hodnotami...

    | Nastavení             | Hodnota                     |
    | ------------------- | ------------------------- |
    | Typ                | CNAME                     |
    | Hostitel                | www                       |
    | Hodnota               | Vložení ze schránky |
    | TTL (Pokud je k dispozici) | Ponechat jako výchozí hodnotu    |

5. Uložte změny u svého poskytovatele DNS.

### <a name="validate-cname"></a>Ověřit CNAME

1. Vraťte se do okna _vlastní domény_ v Azure Portal.

1. Zadejte doménu včetně `www` části v části _ověření vlastní domény_ .

1. Klikněte na tlačítko **ověřit** .

Teď, když je vlastní doména nakonfigurovaná, může zprostředkovatel DNS trvat několik hodin, než se změny rozšíří po celém světě. Stav šíření můžete zjistit tak, že kliknete na [dnspropagation.NET](https://dnspropagation.net). Zadejte vlastní doménu včetně, v `www` rozevíracím seznamu vyberte CNAME a vyberte **Spustit**.

Pokud se vaše změny DNS naplní, web vrátí automaticky generovanou adresu URL vaší statické webové aplikace (například _Random-Name-123456789c.azurestaticapps.NET_).

## <a name="configure-a-root-domain"></a>Konfigurace kořenové domény

Kořenové domény jsou vaší doménou bez subdomény, včetně `www` . Například kořenová doména pro `www.example.com` je `example.com` . Toto je také známo jako doména "APEX".

I když podpora kořenové domény není během verze Preview dostupná, můžete se podívat na Blogový příspěvek [Konfigurace kořenových domén ve službě Azure Static Web Apps](https://burkeholland.github.io/posts/static-app-root-domain) , kde najdete podrobnosti o tom, jak nakonfigurovat podporu kořenové domény pomocí statické webové aplikace.

## <a name="map-a-wildcard-domain"></a>Mapování zástupné domény

Někdy budete chtít, aby byl veškerý provoz odeslaný do subdomény směrován do jiné domény. Běžným příkladem je mapování všech mezidoménových přenosů na `www.example.com` . Tímto způsobem, a to i `w.example.com` v případě, že někdo typ místo `www.example.com` , je žádost odeslána na `www.example.com` .

### <a name="configure-dns-provider"></a>Konfigurace poskytovatele DNS

1. Přihlaste se k webu vašeho poskytovatele domény.

2. Vyhledejte stránku pro správu záznamů DNS. Každý poskytovatel domén má vlastní rozhraní pro správu záznamů DNS, takže se obraťte na dokumentaci poskytovatele. Hledejte oblasti webu označené jako **Domain Name** (Název domény), **DNS** nebo **Name Server Management** (Správa názvových serverů).

3. Často můžete stránku záznamů DNS najít tak, že zobrazíte informace o vašem účtu a vyhledáte odkaz jako **My domains** (Moje domény). Přejít na tuto stránku a vyhledat odkaz s názvem podobným pro **soubor zóny**, **záznamy DNS**nebo **pokročilou konfiguraci**.

    Následující snímek obrazovky obsahuje příklad stránky záznamů DNS:

    :::image type="content" source="media/custom-domain/example-record-ui.png" alt-text="Ukázka konfigurace poskytovatele DNS":::

4. Vytvořte nový záznam **CNAME** s následujícími hodnotami, nahraďte `www.example.com` ho názvem vlastní domény.

    | Nastavení | Hodnota                  |
    | ------- | ---------------------- |
    | Typ    | CNAME                  |
    | Hostitel    | \*                     |
    | Hodnota   | www.example.com        |
    | TTL     | Ponechat jako výchozí hodnotu |

5. Uložte změny u svého poskytovatele DNS.

Když je teď nakonfigurovaná doména se zástupným znakem, může trvat několik hodin, než se změny rozšíří po celém světě. Stav šíření můžete zjistit tak, že kliknete na [dnspropagation.NET](https://dnspropagation.net). Zadejte doménu vlastní domény s jakoukoli subdoménou (jinou než `www` ), v rozevíracím seznamu vyberte CNAME a vyberte **Spustit**.

Pokud se vaše změny DNS naplní, vrátí web vaši vlastní doménu nakonfigurovanou pro vaši statickou webovou aplikaci (například `www.example.com` ).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Konfigurace nastavení aplikace](application-settings.md)

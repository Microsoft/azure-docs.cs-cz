---
title: Hostování webových aplikací Azure s vyrovnáváním zatížení na vrcholu zóny
description: Použití záznamu aliasu Azure DNS k hostování webových aplikací s vyrovnáváním zatížení na vrcholu zóny
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 08/10/2019
ms.author: rohink
ms.openlocfilehash: 8ba96a028d51e6e5503bb4a8e6735b48033c9ba1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937369"
---
# <a name="host-load-balanced-azure-web-apps-at-the-zone-apex"></a>Hostování webových aplikací Azure s vyrovnáváním zatížení na vrcholu zóny

Protokol DNS zabraňuje přiřazení jiného než záznamu A nebo AAAA na vrcholu zóny. Příkladem vrcholu zóny je contoso.com. Toto omezení představuje problém pro vlastníky aplikací, kteří mají aplikace s vyrovnáváním zatížení za Traffic Manager. Není možné ukázat na profil Traffic Manager ze záznamu vrcholu zóny. V důsledku toho musí vlastníci aplikací použít řešení. Přesměrování na aplikační vrstvě musí přesměrovat z vrcholu zóny do jiné domény. Příkladem je přesměrování z contoso.com\.na www contoso.com. Toto uspořádání představuje jeden bod selhání pro funkci přesměrování.

U záznamů aliasu již tento problém neexistuje. Nyní vlastníci aplikace můžete přesměrovat svůj záznam vrcholu zóny na profil Traffic Manager, který má externí koncové body. Vlastníci aplikací mohou překážet na stejný profil Traffic Manageru, který se používá pro jakoukoli jinou doménu v rámci jejich zóny DNS.

Například contoso.com a\.www contoso.com mohou překážet na stejný profil traffic manageru. To je případ, pokud profil Traffic Manager má pouze externí koncové body nakonfigurovány.

V tomto článku se dozvíte, jak vytvořit záznam aliasu pro vrchol domény a nakonfigurovat koncové body profilu Traffic Manageru pro webové aplikace.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

Musíte mít k dispozici název domény, kterou můžete hostovat v Azure DNS a použít k testování. Musíte mít úplnou kontrolu nad touto doménou. Úplná kontrola zahrnuje možnost nastavit pro doménu záznamy názvového serveru (NS).

Pokyny k hostování domény v Azure DNS najdete v [kurzu hostování domény v Azure DNS](dns-delegate-domain-azure-dns.md).

Ukázková doména použitá v tomto kurzu je contoso.com, ale použijte vlastní název domény.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pro uložení všech prostředků použitých v tomto článku.

## <a name="create-app-service-plans"></a>Vytvořit plány služby App Service

Vytvořte dva plány služby Web App Service ve skupině prostředků pomocí následující tabulky pro informace o konfiguraci. Další informace o vytvoření plánu služby App Service najdete [v tématu Správa plánu služby App Service v Azure](../app-service/app-service-plan-manage.md).


|Name (Název)  |Operační systém  |Umístění  |Cenová úroveň  |
|---------|---------|---------|---------|
|ASP-01     |Windows|USA – východ|Vývoj/testování d1-sdílené|
|ASP-02     |Windows|USA – střed|Vývoj/testování d1-sdílené|

## <a name="create-app-services"></a>Vytvořit služby aplikace

Vytvořte dvě webové aplikace, jednu v každém plánu služby App Service.

1. V levém horním rohu stránky portálu Azure vyberte **Vytvořit prostředek**.
2. Na vyhledávacím panelu zadejte **webovou aplikaci** a stiskněte Enter.
3. Vyberte **WebOvou aplikaci**.
4. Vyberte **Vytvořit**.
5. Přijměte výchozí hodnoty a nakonfigurujte dvě webové aplikace v následující tabulce:

   |Name (Název)<br>(musí být jedinečný v rámci .azurewebsites.net)|Skupina prostředků |Zásobník modulu runtime|Region (Oblast)|Plán/umístění služby App Service
   |---------|---------|-|-|-------|
   |Aplikace-01|Použít existující<br>Vyberte skupinu prostředků.|.NET Core 2.2|USA – východ|ASP-01(D1)|
   |Aplikace-02|Použít existující<br>Vyberte skupinu prostředků.|.NET Core 2.2|USA – střed|ASP-02(D1)|

### <a name="gather-some-details"></a>Shromážděte některé podrobnosti

Nyní musíte poznamenat IP adresu a název hostitele webových aplikací.

1. Otevřete skupinu prostředků a vyberte první webovou aplikaci **(App-01** v tomto příkladu).
2. V levém sloupci vyberte **Vlastnosti**.
3. Poznamenejte si adresu uvedenou v **části ADRESA URL**a v části Odchozí adresy **IP** poznamenejte si první adresu IP v seznamu. Tyto informace použijete později při konfiguraci koncových bodů Traffic Manageru.
4. Opakujte pro **aplikaci 02**.

## <a name="create-a-traffic-manager-profile"></a>Vytvoření profilu Traffic Manageru

Vytvořte profil traffic manageru ve skupině prostředků. Použijte výchozí hodnoty a do oboru názvů trafficmanager.net zadejte jedinečný název.

Informace o vytvoření profilu traffic manageru naleznete v [tématu Úvodní příručka: Vytvoření profilu traffic manageru pro vysoce dostupnou webovou aplikaci](../traffic-manager/quickstart-create-traffic-manager-profile.md).

### <a name="create-endpoints"></a>Vytváření koncových bodů

Nyní můžete vytvořit koncové body pro dvě webové aplikace.

1. Otevřete skupinu prostředků a vyberte profil Traffic Manageru.
2. V levém sloupci vyberte **Koncové body**.
3. Vyberte **Přidat**.
4. Koncové body slouží v následující tabulce:

   |Typ  |Name (Název)  |Cíl  |Umístění  |Vlastní nastavení záhlaví|
   |---------|---------|---------|---------|---------|
   |Externí koncový bod     |Konec 01|IP adresa, kterou jste nahráli pro Aplikaci-01|USA – východ|host:\<adresa URL, kterou jste nahráli pro Aplikaci-01\><br>Příklad: **hostitel:01.azurewebsites.net aplikace**|
   |Externí koncový bod     |Konec 02|IP adresa, kterou jste nahráli pro Aplikaci 02|USA – střed|host:\<adresa URL, kterou jste nahráli pro Aplikaci-02\><br>Příklad: **hostitel:02.azurewebsites.net**

## <a name="create-dns-zone"></a>Vytvořit zónu DNS

K testování můžete použít existující zónu DNS nebo vytvořit novou zónu. Pokud chcete vytvořit a delegovat novou zónu DNS v Azure, [přečtěte si informace o tom, že hostujete svou doménu ve službě Azure DNS](dns-delegate-domain-azure-dns.md).

## <a name="add-a-txt-record-for-custom-domain-validation"></a>Přidání záznamu TXT pro ověření vlastní domény

Když do webových aplikací přidáte vlastní název hostitele, vyhledá konkrétní txt záznam, který ověří vaši doménu.

1. Otevřete skupinu prostředků a vyberte zónu DNS.
2. Vyberte **Sada záznamů**.
3. Přidejte sadu záznamů pomocí následující tabulky. Pro tuto hodnotu použijte adresu URL skutečné webové aplikace, kterou jste dříve zaznamenali:

   |Name (Název)  |Typ  |Hodnota|
   |---------|---------|-|
   |@     |TXT|App-01.azurewebsites.net|


## <a name="add-a-custom-domain"></a>Přidání vlastní domény

Přidejte vlastní doménu pro obě webové aplikace.

1. Otevřete skupinu prostředků a vyberte první webovou aplikaci.
2. V levém sloupci vyberte **Vlastní domény**.
3. V části **Vlastní domény**vyberte Přidat **vlastní doménu**.
4. V části **Vlastní doména**zadejte vlastní název domény. Například contoso.com.
5. Vyberte **Ověřit**.

   Vaše doména by měla projít ověřením a zobrazit zelené zaškrtnutí vedle **dostupnosti Hostname** a **vlastnictví domény**.
5. Vyberte **Přidat vlastní doménu**.
6. Chcete-li zobrazit nový název hostitele v části **Názvy hostitelů přiřazené k webu**, aktualizujte prohlížeč. Aktualizace na stránce nezobrazuje vždy změny hned.
7. Tento postup opakujte pro druhou webovou aplikaci.

## <a name="add-the-alias-record-set"></a>Přidání sady záznamů aliasu

Nyní přidejte záznam aliasu pro vrchol zóny.

1. Otevřete skupinu prostředků a vyberte zónu DNS.
2. Vyberte **Sada záznamů**.
3. Přidejte sadu záznamů pomocí následující tabulky:

   |Name (Název)  |Typ  |Sada záznamů aliasu  |Typ aliasu  |Prostředek Azure|
   |---------|---------|---------|---------|-----|
   |@     |A|Ano|Prostředek Azure|Traffic Manager - váš profil|


## <a name="test-your-web-apps"></a>Testování webových aplikací

Nyní můžete otestovat, abyste se ujistili, že se můžete dostat do webové aplikace a že je vyvážená zatížením.

1. Otevřete webový prohlížeč a vyhledejte svou doménu. Například contoso.com. Měla by se zobrazit výchozí stránka webové aplikace.
2. Zastavte svou první webovou aplikaci.
3. Zavřete webový prohlížeč a počkejte několik minut.
4. Spusťte webový prohlížeč a vyhledejte svou doménu. Stále byste měli vidět výchozí stránku webové aplikace.
5. Zastavte druhou webovou aplikaci.
6. Zavřete webový prohlížeč a počkejte několik minut.
7. Spusťte webový prohlížeč a vyhledejte svou doménu. Měla by se zobrazit chyba 403 označující, že webová aplikace je zastavena.
8. Spusťte druhou webovou aplikaci.
9. Zavřete webový prohlížeč a počkejte několik minut.
10. Spusťte webový prohlížeč a vyhledejte svou doménu. Měla by se znovu zobrazit výchozí stránka webové aplikace.

## <a name="next-steps"></a>Další kroky

Další informace o záznamech aliasů najdete v následujících článcích:

- [Kurz: Konfigurace záznamu aliasu tak, aby odkazoval na veřejnou IP adresu Azure](tutorial-alias-pip.md)
- [Kurz: Konfigurace záznamu aliasu pro podporu vrcholů názvů domén ve službě Traffic Manager](tutorial-alias-tm.md)
- [Nejčastější dotazy k DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)

Informace o migraci aktivního názvu DNS najdete [v tématu Migrace aktivního názvu DNS do služby Azure App Service](../app-service/manage-custom-dns-migrate-domain.md).

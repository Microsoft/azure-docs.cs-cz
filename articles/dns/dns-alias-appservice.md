---
title: Webové aplikace Azure s vyrovnáváním zatížení hostitele ve vrcholu zóny
description: Použijte záznam aliasu Azure DNS k hostování webových aplikací s vyrovnáváním zatížení ve vrcholu zóny.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 08/10/2019
ms.author: rohink
ms.openlocfilehash: e7c4db7a2fc3ba931415e3b167f7fe72ee2b3980
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84710537"
---
# <a name="host-load-balanced-azure-web-apps-at-the-zone-apex"></a>Webové aplikace Azure s vyrovnáváním zatížení hostitele ve vrcholu zóny

Protokol DNS brání přiřazení jakékoli jiné než záznam A nebo AAAA na vrcholu zóny. Vzorový vrchol zóny je contoso.com. Toto omezení představuje problém pro vlastníky aplikace, kteří mají aplikace s vyrovnáváním zatížení za Traffic Manager. Není možné nasměrovat profil Traffic Manager ze záznamu vrcholu zóny. V důsledku toho musí vlastníci aplikace používat alternativní řešení. Přesměrování v aplikační vrstvě se musí přesměrovat ze vrcholu zóny na jinou doménu. Příkladem je přesměrování z contoso.com na webovou \. contoso.com. Toto uspořádání představuje pro funkci přesměrování jediný bod selhání.

U záznamů aliasů tento problém již neexistuje. Vlastníci aplikací teď můžou odkazovat na svůj záznam vrcholu zóny na Traffic Manager profil, který má externí koncové body. Vlastníci aplikací můžou odkazovat na stejný profil Traffic Manager, který se používá pro jakoukoliv jinou doménu v rámci zóny DNS.

Například contoso.com a webová \. contoso.com mohou odkazovat na stejný profil Traffic Manager. Toto je případ, pokud má profil Traffic Manager jenom nakonfigurované externí koncové body.

V tomto článku se dozvíte, jak vytvořit záznam aliasu pro svůj doménový vrchol a nakonfigurovat koncové body profilu Traffic Manager pro vaše webové aplikace.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Musíte mít k dispozici název domény, kterou můžete hostovat v Azure DNS a použít k testování. Musíte mít úplnou kontrolu nad touto doménou. Úplná kontrola zahrnuje možnost nastavit pro doménu záznamy názvového serveru (NS).

Pokyny k hostování domény v Azure DNS najdete v [kurzu hostování domény v Azure DNS](dns-delegate-domain-azure-dns.md).

Ukázková doména použitá v tomto kurzu je contoso.com, ale použijte vlastní název domény.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků, která bude obsahovat všechny prostředky používané v tomto článku.

## <a name="create-app-service-plans"></a>Vytvoření plánů App Service

Vytvořte dva plány Web App Service ve skupině prostředků pomocí následující tabulky pro informace o konfiguraci. Další informace o vytvoření plánu App Service najdete v tématu [Správa plánu App Service v Azure](../app-service/app-service-plan-manage.md).


|Name  |Operační systém  |Umístění  |Cenová úroveň  |
|---------|---------|---------|---------|
|ASP – 01     |Windows|East US|D1-Shared pro vývoj a testování|
|ASP-02     |Windows|Střední USA|D1-Shared pro vývoj a testování|

## <a name="create-app-services"></a>Vytvořit App Services

Vytvořte dvě webové aplikace, jednu v každém plánu App Service.

1. V levém horním rohu stránky Azure Portal vyberte **vytvořit prostředek**.
2. Do vyhledávacího panelu zadejte **Web App** a stiskněte klávesu ENTER.
3. Vyberte **Webová aplikace**.
4. Vyberte **Vytvořit**.
5. Přijměte výchozí hodnoty a pomocí následující tabulky nakonfigurujte dvě webové aplikace:

   |Name<br>(musí být jedinečné v rámci. azurewebsites.net)|Resource Group |Zásobník modulu runtime|Oblast|App Service plán/umístění
   |---------|---------|-|-|-------|
   |App – 01|Použít existující<br>Vyberte skupinu prostředků.|.NET Core 2.2|East US|ASP-01 (D1)|
   |App-02|Použít existující<br>Vyberte skupinu prostředků.|.NET Core 2.2|Střední USA|ASP-02 (D1)|

### <a name="gather-some-details"></a>Shromáždit nějaké podrobnosti

Teď je potřeba poznamenat IP adresu a název hostitele pro webové aplikace.

1. Otevřete skupinu prostředků a v tomto příkladu vyberte svou první webovou aplikaci (**App-01** ).
2. V levém sloupci vyberte možnost **vlastnosti**.
3. Poznamenejte si adresu pod **adresou URL**a v části **odchozí IP adresy** si všimněte první IP adresy v seznamu. Tyto informace použijete později při konfiguraci Traffic Manager koncových bodů.
4. Opakujte pro **App-02**.

## <a name="create-a-traffic-manager-profile"></a>Vytvoření profilu Traffic Manageru

Vytvořte profil Traffic Manager ve skupině prostředků. Použijte výchozí hodnoty a zadejte jedinečný název v rámci oboru názvů trafficmanager.net.

Informace o vytvoření profilu Traffic Manager najdete v tématu [rychlý Start: vytvoření profilu Traffic Manager pro webovou aplikaci s vysokou dostupností](../traffic-manager/quickstart-create-traffic-manager-profile.md).

### <a name="create-endpoints"></a>Vytváření koncových bodů

Nyní můžete vytvořit koncové body pro tyto dvě webové aplikace.

1. Otevřete skupinu prostředků a vyberte profil Traffic Manager.
2. V levém sloupci vyberte **koncové body**.
3. Vyberte **Přidat**.
4. Pro konfiguraci koncových bodů použijte následující tabulku:

   |Typ  |Name  |Cíl  |Umístění  |Vlastní nastavení hlaviček|
   |---------|---------|---------|---------|---------|
   |Externí koncový bod     |Konec – 01|IP adresa, kterou jste si poznamenali pro App-01|East US|provoz\<the URL you recorded for App-01\><br>Příklad: **Host: App-01.azurewebsites.NET**|
   |Externí koncový bod     |Konec-02|IP adresa, kterou jste si poznamenali pro App-02|Střední USA|provoz\<the URL you recorded for App-02\><br>Příklad: **Host: App-02.azurewebsites.NET**

## <a name="create-dns-zone"></a>Vytvořit zónu DNS

Můžete použít buď existující zónu DNS pro testování, nebo můžete vytvořit novou zónu. Informace o vytvoření a delegování nové zóny DNS v Azure najdete v tématu [kurz: Hostování vaší domény v Azure DNS](dns-delegate-domain-azure-dns.md).

## <a name="add-a-txt-record-for-custom-domain-validation"></a>Přidat záznam TXT pro ověření vlastní domény

Když do svých webových aplikací přidáte vlastní název hostitele, bude vyhledán konkrétní záznam TXT pro ověření vaší domény.

1. Otevřete skupinu prostředků a vyberte zónu DNS.
2. Vyberte **Sada záznamů**.
3. Přidejte sadu záznamů pomocí následující tabulky. Pro tuto hodnotu použijte skutečnou adresu URL webové aplikace, kterou jste předtím nahráli:

   |Název  |Typ  |Hodnota|
   |---------|---------|-|
   |@     |TXT|App-01.azurewebsites.net|


## <a name="add-a-custom-domain"></a>Přidání vlastní domény

Přidejte vlastní doménu pro obě webové aplikace.

1. Otevřete skupinu prostředků a vyberte svou první webovou aplikaci.
2. V levém sloupci vyberte **vlastní domény**.
3. V části **vlastní domény**vyberte **Přidat vlastní doménu**.
4. V části **vlastní doména**zadejte vlastní název domény. Například contoso.com.
5. Vyberte **Ověřit**.

   Vaše doména by měla projít ověřením a zobrazit zelenou značku zaškrtnutí u **názvu hostitele** a **doménového vlastnictví**.
5. Vyberte **Přidat vlastní doménu**.
6. Pokud chcete zobrazit nový název hostitele v části **názvy hostitele přiřazené k lokalitě**, aktualizujte si prohlížeč. Aktualizace na stránce vždy okamžitě nezobrazuje změny.
7. Tento postup opakujte pro druhou webovou aplikaci.

## <a name="add-the-alias-record-set"></a>Přidat sadu záznamů aliasů

Nyní přidejte záznam aliasu pro vrchol zóny.

1. Otevřete skupinu prostředků a vyberte zónu DNS.
2. Vyberte **Sada záznamů**.
3. Přidejte sadu záznamů pomocí následující tabulky:

   |Název  |Typ  |Sada záznamů aliasů  |Typ aliasu  |Prostředek Azure|
   |---------|---------|---------|---------|-----|
   |@     |A|Yes|Prostředek Azure|Traffic Manager – váš profil|


## <a name="test-your-web-apps"></a>Testování webových aplikací

Nyní můžete testovat, abyste se ujistili, že máte přístup k webové aplikaci a že je vyrovnávání zatížení.

1. Otevřete webový prohlížeč a přejděte k doméně. Například contoso.com. Měla by se zobrazit stránka výchozí webová aplikace.
2. Zastavte svou první webovou aplikaci.
3. Zavřete webový prohlížeč a počkejte několik minut.
4. Spusťte webový prohlížeč a přejděte k doméně. Pořád se zobrazí výchozí stránka webové aplikace.
5. Zastavte druhou webovou aplikaci.
6. Zavřete webový prohlížeč a počkejte několik minut.
7. Spusťte webový prohlížeč a přejděte k doméně. Měla by se zobrazit Chyba 403, která indikuje, že webová aplikace je zastavená.
8. Spusťte druhou webovou aplikaci.
9. Zavřete webový prohlížeč a počkejte několik minut.
10. Spusťte webový prohlížeč a přejděte k doméně. Měla by se zobrazit výchozí stránka webové aplikace.

## <a name="next-steps"></a>Další kroky

Další informace o záznamech aliasů najdete v následujících článcích:

- [Kurz: Konfigurace záznamu aliasu, který odkazuje na veřejnou IP adresu Azure](tutorial-alias-pip.md)
- [Kurz: Konfigurace záznamu aliasu pro podporu vrcholů názvů domén ve službě Traffic Manager](tutorial-alias-tm.md)
- [Nejčastější dotazy k DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)

Informace o tom, jak migrovat aktivní název DNS, najdete v tématu [migrace aktivního názvu DNS na Azure App Service](../app-service/manage-custom-dns-migrate-domain.md).

---
title: Hostitele s vyrovnáváním zatížení Azure web apps ve vrcholu zóny
description: Použití Azure DNS alias záznamu na hostitele s vyrovnáváním zatížení webové aplikace ve vrcholu zóny
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 11/3/2018
ms.author: victorh
ms.openlocfilehash: 2b14753237e118540da6306fa9f06816f3e58b71
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2018
ms.locfileid: "50979702"
---
# <a name="host-load-balanced-azure-web-apps-at-the-zone-apex"></a>Hostitele s vyrovnáváním zatížení Azure web apps ve vrcholu zóny

Protokol DNS brání přiřazení čehokoli jiného, než záznam A nebo AAAA ve vrcholu zóny. Vrcholu zóny příklad je contoso.com. Toto omezení představuje problém pro počet vlastníků aplikace, kteří mají aplikace s vyrovnáváním zatížení za Traffic Manager. Není možné tak, aby odkazoval na profil Traffic Manageru z vrcholu zóny. Počet vlastníků aplikace v důsledku toho musíte použít alternativní řešení. Přesměrování na aplikační vrstvě musí přesměrovat adresu z vrcholu zóny na jinou doménu. Je například přesměrování z contoso.com na www.contoso.com. Toto uspořádání představuje jediný bod selhání pro funkci přesměrování.

Pomocí záznamů aliasů tento problém již neexistuje. Počet vlastníků aplikace teď může odkazovat jejich záznamu vrcholu zóny na profil Traffic Manageru, který má externí koncové body. Počet vlastníků aplikace může odkazovat na stejný profil Traffic Manageru, který se používá u všech ostatních domén v rámci jejich zóny DNS.

Například contoso.com a www.contoso.com může odkazovat na stejný profil Traffic Manageru. To platí za předpokladu, profil služby Traffic Manager má jenom externí nakonfigurované koncové body.

V tomto článku se dozvíte, jak vytvořit záznam aliasu pro vaši doménu vrcholu a nakonfigurujte vaše koncové body Traffic Manageru profil pro svoje webové aplikace.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Musíte mít k dispozici název domény, kterou můžete hostovat v Azure DNS a použít k testování. Musí mít úplnou kontrolu nad tuto doménu. Úplné řízení zahrnuje možnost nastavit název serveru (NS) záznamy pro doménu.

Pokyny k hostování domény v Azure DNS najdete v [kurzu hostování domény v Azure DNS](dns-delegate-domain-azure-dns.md).

Příklad doménu, kterou používá pro účely tohoto kurzu je contoso.com, ale použít vlastní název domény.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pro všechny prostředky používané v tomto článku.

## <a name="create-app-service-plans"></a>Vytvoření plánů služby App Service

Vytvořte dva plány služby App Service Web ve vaší skupině prostředků, informace o konfiguraci pomocí následující tabulky. Další informace o vytvoření plánu služby App Service najdete v tématu [spravovat plán služby App Service v Azure](../app-service/app-service-plan-manage.md).


|Název  |Operační systém  |Umístění  |Cenová úroveň  |
|---------|---------|---------|---------|
|ASP-01     |Windows|USA – východ|Sdílené D1 pro vývoj/testování|
|ASP-02     |Windows|USA – střed|Sdílené D1 pro vývoj/testování|

## <a name="create-app-services"></a>Vytvořit službu App Services

Vytvořte dvě webové aplikace, jeden v jednotlivých plánech služby App Service.

1. V levém horním rohu stránky Azure portal, klikněte na **vytvořit prostředek**.
2. Typ **webovou aplikaci** na panelu hledání a potom stiskněte klávesu Enter.
3. Klikněte na tlačítko **webová aplikace**.
4. Klikněte na možnost **Vytvořit**.
5. Přijměte výchozí hodnoty a v následující tabulce použít ke konfiguraci dvě webové aplikace:

   |Název<br>(musí být jedinečný v rámci. azurewebsites.net)|Skupina prostředků |Plán služby App Service/umístění
   |---------|---------|---------|
   |App-01|Použít existující<br>Vyberte skupinu prostředků|ASP 01(East US)|
   |App-02|Použít existující<br>Vyberte skupinu prostředků|ASP 02(Central US)|

### <a name="gather-some-details"></a>Shromažďovat některé podrobnosti

Teď je potřeba si poznamenejte IP adresu a hostitele název aplikace.

1. Otevřete skupinu prostředků a klikněte na svoji první aplikaci (**aplikace-01** v tomto příkladu).
2. V levém sloupci klikněte na tlačítko **vlastnosti**.
3. Poznamenejte si adresu v rámci **URL**a v části **odchozí IP adresy** mějte na paměti první IP adresa v seznamu. Použijete tyto informace později při konfiguraci vašich koncových bodů Traffic Manageru.
4. Opakujte pro **aplikace-02**.

## <a name="create-a-traffic-manager-profile"></a>Vytvoření profilu Traffic Manageru

Vytvořte profil služby Traffic Manager ve vaší skupině prostředků. Použít výchozí hodnoty a zadejte jedinečný název v rámci oboru názvů trafficmanager.net.

Informace o vytvoření profilu Traffic Manageru najdete v tématu [rychlý start: vytvoření profilu Traffic Manageru s vysokou dostupností webové aplikace](../traffic-manager/quickstart-create-traffic-manager-profile.md).

### <a name="create-endpoints"></a>Vytváření koncových bodů

Nyní můžete vytvořit koncové body pro dvě webové aplikace.

1. Otevřete skupinu prostředků a klikněte na svůj profil Traffic Manageru.
2. V levém sloupci klikněte na tlačítko **koncové body**.
3. Klikněte na tlačítko **Add** (Přidat).
4. V následující tabulce použijte ke konfiguraci koncových bodů:

   |Typ  |Název  |Cíl  |Umístění  |Vlastní nastavení hlaviček|
   |---------|---------|---------|---------|---------|
   |Externí koncový bod     |End-01|IP adresa, kterou jste si poznamenali pro App-01|USA – východ|Hostitel:\<adresu URL, které jste si poznamenali pro App-01\><br>Příklad: **hostitele: aplikace-01.azurewebsites.net**|
   |Externí koncový bod     |End-02|IP adresa, kterou jste si poznamenali pro App-02|USA – střed|Hostitel:\<adresu URL, které jste si poznamenali pro App-02\><br>Příklad: **hostitele: aplikace-02.azurewebsites.net**

## <a name="create-dns-zone"></a>Vytvoření zóny DNS

Existující zónu DNS můžete použít pro testování, nebo můžete vytvořit novou zónu. Vytvoření a delegování novou zónu DNS v Azure najdete v tématu [kurz: hostování domény v Azure DNS](dns-delegate-domain-azure-dns.md).

### <a name="add-the-alias-record-set"></a>Přidat sadu záznamů alias

Pokud zónu DNS je připraven, můžete přidat záznamu o aliasu pro vrcholu zóny.

1. Otevřete skupinu prostředků a klikněte na zónu DNS.
2. Klikněte na **Sada záznamů**.
3. Přidání záznamu, nastavte pomocí následující tabulky:

   |Název  |Typ  |Sada záznamů alias  |Typ aliasu  |Prostředek Azure|
   |---------|---------|---------|---------|-----|
   |@     |A|Ano|Prostředek Azure|Traffic Manager – profilu|

## <a name="add-custom-hostnames"></a>Přidat vlastní názvy hostitelů

Přidáte vlastní název hostitele pro obě webové aplikace.

1. Otevřete skupinu prostředků a klikněte na první webové aplikace.
2. V levém sloupci klikněte na tlačítko **vlastní domény**.
3. Klikněte na tlačítko **přidat název hostitele**.
4. V části název hostitele zadejte název vaší domény. Např. contoso.com.

   Vaše doména by měl projít ověřením a zobrazí zelenou značku zaškrtnutí vedle **dostupnost názvu hostitele** a **vlastnictví domény**.
5. Klikněte na tlačítko **přidat název hostitele**.
6. Chcete-li zobrazit nový název hostitele v části **názvy hostitele přiřazené lokality**, aktualizujte svůj prohlížeč. Aktualizace na stránce vždy nezobrazuje změny okamžitě.
7. Tento postup opakujte pro druhou webovou aplikaci.

## <a name="test-your-web-apps"></a>Testování webových aplikací

Nyní můžete otestovat a ujistěte se, že můžete oslovit vaší webové aplikace a že se zatížení rovnoměrně.

1. Otevřete webový prohlížeč a přejděte k vaší doméně. Např. contoso.com. Zobrazí se výchozí webová stránka aplikace.
2. Zastavte vaší první webové aplikace.
3. Zavřete webový prohlížeč a počkejte několik minut.
4. Spustit webový prohlížeč a přejděte k vaší doméně. Zobrazí se stále výchozí webové stránky aplikace.
5. Zastavte druhou webovou aplikaci.
6. Zavřete webový prohlížeč a počkejte několik minut.
7. Spustit webový prohlížeč a přejděte k vaší doméně. Zobrazí se Chyba 403, která udává, že webová aplikace zastavená.
8. Spusťte druhou webovou aplikaci.
9. Zavřete webový prohlížeč a počkejte několik minut.
10. Spustit webový prohlížeč a přejděte k vaší doméně. Měli byste vidět výchozí webová stránka aplikace znovu.

## <a name="next-steps"></a>Další postup

Další informace o záznamů aliasů, naleznete v následujících článcích:

- [Kurz: Konfigurace záznamu o aliasu odkazovat na veřejnou IP adresu Azure](tutorial-alias-pip.md)
- [Kurz: Konfigurace záznamu o aliasu pro podporu vrcholu názvy domén s Traffic Managerem](tutorial-alias-tm.md)
- [Nejčastější dotazy k DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)

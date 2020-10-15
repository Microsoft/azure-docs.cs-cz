---
title: Konfigurace vlastního názvu domény v Cloud Services | Microsoft Docs
description: Pomocí konfigurace nastavení DNS se dozvíte, jak vystavit aplikaci nebo data Azure na internetu na vlastní doméně.  V těchto příkladech se používá Azure Portal.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: tagore
ms.openlocfilehash: 012801d0aada8ee55bb0eb05eaf75caa95878765
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92069921"
---
# <a name="configuring-a-custom-domain-name-for-an-azure-cloud-service"></a>Konfigurace vlastního názvu domény pro cloudovou službu Azure
Když vytvoříte cloudovou službu, Azure ji přiřadí subdoménou **cloudapp.NET**. Pokud se například vaše cloudová služba jmenuje "contoso", budou mít vaši uživatelé přístup k vaší aplikaci na adrese URL, jako je `http://contoso.cloudapp.net` . Azure také přiřadí virtuální IP adresu.

Svou aplikaci ale můžete zveřejnit i na vlastním názvu domény, jako je třeba **contoso.com**. Tento článek vysvětluje, jak vyhradit nebo nakonfigurovat vlastní název domény pro webové role cloudové služby.

Už rozumíte záznamům CNAME a a záznamů? [Přeskočit předchozí vysvětlení](#add-a-cname-record-for-your-custom-domain).

> [!NOTE]
> Postupy v této úloze se vztahují na Azure Cloud Services. App Services najdete v tématu [Mapování existujícího vlastního názvu DNS na Azure Web Apps](../app-service/app-service-web-tutorial-custom-domain.md). Účty úložiště najdete v tématu [Konfigurace vlastního názvu domény pro koncový bod služby Azure Blob Storage](../storage/blobs/storage-custom-domain-name.md).
> 
> 

<p/>

> [!TIP]
> Rychlejší zprovoznění – použijte nový [návod Průvodce](https://support.microsoft.com/kb/2990804)Azure.  Díky tomu přidružuje vlastní název domény a zabezpečení komunikace (TLS) k Azure Cloud Services nebo Azure websites.
> 
> 

## <a name="understand-cname-and-a-records"></a>Pochopení záznamů CNAME a záznamů
Záznamy CNAME (nebo aliasy) a záznamy umožňují přidružit název domény k určitému serveru (nebo službě v tomto případě), ale pracují jinak. Při použití záznamů se službou Azure Cloud Services, které byste měli zvážit, je potřeba zvážit také určité okolnosti, než se rozhodnete, které chcete použít.

### <a name="cname-or-alias-record"></a>Záznam CNAME nebo alias
Záznam CNAME mapuje *konkrétní* doménu, například **contoso.com** nebo **www \. contoso.com**, na kanonický název domény. V tomto případě je kanonický název domény název domény **[MyApp]. cloudapp. NET** vaší hostované aplikace Azure. Po vytvoření CNAME vytvoří alias pro: **[MyApp]. cloudapp. NET**. Záznam CNAME se přeloží na IP adresu vaší **[MyApp]. cloudapp. NET** Service, takže pokud se IP adresa cloudové služby změní, nemusíte provádět žádnou akci.

> [!NOTE]
> Někteří registrátori domén umožňují mapovat subdomény jenom při použití záznamu CNAME, jako je například \. contoso.com www, a ne kořenových názvů, jako je například contoso.com. Další informace o záznamech CNAME najdete v dokumentaci poskytnuté registrátorem, v [záznamu Wikipedii záznamu CNAME](https://en.wikipedia.org/wiki/CNAME_record)nebo v dokumentu [název domény IETF – implementace a specifikace](https://tools.ietf.org/html/rfc1035) .

### <a name="a-record"></a>Záznam A
Záznam *A* mapuje doménu, jako je například **contoso.com** nebo **Webová \. contoso.com**, *nebo doména se zástupnými znaky* , jako je například ** \* . contoso.com**, na IP adresu. V případě cloudové služby Azure je virtuální IP adresa služby. Hlavní výhodou záznamu A v rámci záznamu CNAME je, že můžete mít jednu položku, která používá zástupný znak, například \* **. contoso.com**, který zpracovává požadavky na více dílčích domén, jako je **mail.contoso.com**, **Login.contoso.com**nebo **Webová \. contso.com**.

> [!NOTE]
> Vzhledem k tomu, že je záznam A namapován na statickou IP adresu, nemůže automaticky vyřešit změny IP adresy vaší cloudové služby. IP adresa, kterou používá vaše cloudová služba, se přidělí při prvním nasazení do prázdného slotu (výroba nebo příprava). Pokud odstraníte nasazení pro slot, IP adresa se uvolní v Azure a jakékoli budoucí nasazení do slotu se může předávat nové IP adrese.
> 
> V takovém případě se IP adresa daného slotu nasazení (výroba nebo příprava) zachová při výměně mezi přípravnými a provozními nasazeními nebo s prováděním místního upgradu stávajícího nasazení. Další informace o provádění těchto akcí najdete v tématu [Správa Cloud Services](cloud-services-how-to-manage-portal.md).
> 
> 

## <a name="add-a-cname-record-for-your-custom-domain"></a>Přidat záznam CNAME pro vlastní doménu
Chcete-li vytvořit záznam CNAME, je nutné přidat novou položku do tabulky DNS pro vlastní doménu pomocí nástrojů poskytovaných vaším registrátorem. Každý registrátor má podobnou, ale mírně odlišnou metodu určení záznamu CNAME, ale koncepty jsou stejné.

1. Pomocí jedné z těchto metod můžete najít název domény **. cloudapp.NET** přiřazený ke cloudové službě.

   * Přihlaste se k [Azure Portal], vyberte cloudovou službu, podívejte se do části **Přehled** a vyhledejte položku **Adresa URL webu** .

       ![část rychlý přehled znázorňující adresu URL webu][csurl]

       **OR**
   * Nainstalujte a nakonfigurujte [Azure PowerShell](/powershell/azure/)a pak použijte tento příkaz:

       ```powershell
       Get-AzureDeployment -ServiceName yourservicename | Select Url
       ```

     Uložte název domény, který se používá v adrese URL vrácené kteroukoli metodou, jak ho budete potřebovat při vytváření záznamu CNAME.
2. Přihlaste se k webu registrátora DNS a na stránce pro správu DNS se můžete dostat na stránku. Vyhledejte odkazy nebo oblasti webu označené jako **název domény**, **DNS**nebo **Správa názvového serveru**.
3. Nyní zjistíte, kde můžete vybrat nebo zadat záznam CNAME. Možná budete muset vybrat typ záznamu z rozevírací nabídky nebo přejít na stránku pokročilá nastavení. Měli byste hledat slova **CNAME**, **alias**nebo **subdomény**.
4. Pokud chcete vytvořit alias pro **webovou \. customdomain.com**, musíte zadat také alias domény nebo subdomény pro záznam CNAME, jako je například **www** . Pokud chcete vytvořit alias pro kořenovou doménu, může být uvedený jako **\@** symbol v nástrojích DNS vašeho registrátora.
5. Pak musíte zadat kanonický název hostitele, který je v tomto případě **cloudapp.NET** doménou vaší aplikace.

Například následující záznam CNAME přepošle veškerý provoz z **webové \. contoso.com** do **contoso.cloudapp.NET**, vlastní název domény nasazené aplikace:

| Alias/název hostitele/subdoména | Kanonická doména |
| --- | --- |
| www |contoso.cloudapp.net |

> [!NOTE]
> Návštěvník **webové \. contoso.com** nikdy neuvidí tohoto skutečného hostitele (contoso.cloudapp.NET), takže proces předávání nebude pro koncového uživatele viditelný.
> 
> Výše uvedený příklad se vztahuje pouze na provoz v subdoméně **webové** služby. Vzhledem k tomu, že nemůžete použít zástupné znaky se záznamy CNAME, je nutné pro každou doménu nebo subdoménu vytvořit jeden záznam CNAME. Pokud chcete směrovat provoz z subdomén, jako je například *. contoso.com, do vaší cloudapp.net adresy, můžete nakonfigurovat **přesměrování adresy URL** nebo přesměrovat **adresu URL** v nastavení DNS nebo vytvořit záznam a.

## <a name="add-an-a-record-for-your-custom-domain"></a>Přidat záznam A pro vlastní doménu
Pokud chcete vytvořit záznam A, musíte nejdřív najít virtuální IP adresu vaší cloudové služby. Pak přidejte novou položku do tabulky DNS pro vlastní doménu pomocí nástrojů poskytovaných vaším registrátorem. Každý registrátor má podobnou, ale mírně odlišnou metodu určení záznamu A, ale koncepty jsou stejné.

1. K získání IP adresy vaší cloudové služby použijte jednu z následujících metod.

   * Přihlaste se k [Azure Portal], vyberte cloudovou službu, podívejte se do části **Přehled** a vyhledejte položku **veřejné IP adresy** .

       ![část rychlý přehled znázorňující virtuální IP adresu][vip]

       **OR**
   * Nainstalujte a nakonfigurujte [Azure PowerShell](/powershell/azure/)a pak použijte tento příkaz:

       ```powershell
       get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
       ```

     Uložte IP adresu, protože ji budete potřebovat při vytváření záznamu A.
2. Přihlaste se k webu registrátora DNS a na stránce pro správu DNS se můžete dostat na stránku. Vyhledejte odkazy nebo oblasti webu označené jako **název domény**, **DNS**nebo **Správa názvového serveru**.
3. Nyní zjistíte, kde můžete vybrat nebo zadat záznam. Možná budete muset vybrat typ záznamu z rozevírací nabídky nebo přejít na stránku pokročilá nastavení.
4. Vyberte nebo zadejte doménu nebo subdoménu, které budou používat tento záznam. Pokud například chcete vytvořit alias pro **webovou \. customdomain.com**, vyberte možnost **www** . Pokud chcete vytvořit položku se zástupnými znaky pro všechny subdomény, zadejte * * * * *. To se bude vztahovat na všechny poddomény, jako jsou **mail.customdomain.com**, **login.customdomain.com**a **www \. customdomain.com**.

    Pokud chcete vytvořit záznam A pro kořenovou doménu, může být uvedený jako **\@** symbol v nástrojích DNS vašeho registrátora.
5. Do poskytnutého pole zadejte IP adresu vaší cloudové služby. Tím přidružíte položku domény použitou v záznamu A s IP adresou nasazení cloudové služby.

Například následující záznam přepošle veškerý provoz z **contoso.com** do **137.135.70.239**, IP adresu nasazené aplikace:

| Název hostitele nebo subdoména | IP adresa |
| --- | --- |
| \@ |137.135.70.239 |

Tento příklad ukazuje vytvoření záznamu A pro kořenovou doménu. Pokud chcete vytvořit položku se zástupnými znaky pro pokrytí všech subdomén, zadejte jako subdoménu * * * * *.

> [!WARNING]
> IP adresy v Azure jsou ve výchozím nastavení dynamické. Pravděpodobně budete chtít použít [vyhrazenou IP adresu](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) , abyste se ujistili, že se vaše IP adresa nemění.
> 
> 

## <a name="next-steps"></a>Další kroky
* [Správa Cloud Services](cloud-services-how-to-manage-portal.md)
* [Postup mapování obsahu CDN do vlastní domény](../cdn/cdn-map-content-to-custom-domain.md)
* [Obecná konfigurace cloudové služby](cloud-services-how-to-configure-portal.md)
* Přečtěte si, jak [nasadit cloudovou službu](cloud-services-how-to-create-deploy-portal.md).
* Nakonfigurujte [certifikáty TLS/SSL](cloud-services-configure-ssl-certificate-portal.md).

[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: cloud-services-how-to-manage-portal.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[Azure Portal]: https://portal.azure.com
[vip]: ./media/cloud-services-custom-domain-name-portal/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name-portal/csurl.png
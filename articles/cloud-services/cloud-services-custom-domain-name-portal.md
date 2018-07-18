---
title: Konfigurace vlastního názvu domény v cloudových službách | Dokumentace Microsoftu
description: Zjistěte, jak vystavit data k Internetu pro vlastní doménu vaší aplikace Azure tím, že nakonfigurujete nastavení DNS.  Tyto příklady pomocí webu Azure portal.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 5783a246-a151-4fb1-b488-441bfb29ee44
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeconnoc
ms.openlocfilehash: 43a3458ba0f08d73931b5ecddf6a2c7b1ae259f4
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39044811"
---
# <a name="configuring-a-custom-domain-name-for-an-azure-cloud-service"></a>Konfigurace vlastního názvu domény pro cloudovou službu Azure
Při vytváření cloudové služby, Azure ho přiřadí na subdoménu **cloudapp.net**. Například pokud vaši Cloudovou službu se s názvem "contoso", uživatelé budou moct získat přístup k vaší aplikaci na adrese URL jako http://contoso.cloudapp.net. Azure také přiřadí virtuální IP adresu.

Ale můžete také zveřejnit aplikaci na svůj vlastní název domény, jako **contoso.com**. Tento článek vysvětluje, jak rezervovat nebo konfiguraci vlastního názvu domény pro Cloudovou službu webové role.

Už rozumíte co CNAME záznamy a A jsou? [Jump minulé vysvětlení](#add-a-cname-record-for-your-custom-domain).

> [!NOTE]
> Postupy v této úloze lze použít pro Azure Cloud Services. Aplikační služby, najdete v části [mapování existujícího vlastního názvu DNS na Azure Web Apps](../app-service/app-service-web-tutorial-custom-domain.md). Účty úložiště, najdete v části [konfigurace vlastního názvu domény pro koncový bod služby Azure Blob storage](../storage/blobs/storage-custom-domain-name.md).
> 
> 

<p/>

> [!TIP]
> Naučte se pracovat rychleji – použijte Azure NOVĚ [názorný postup](http://support.microsoft.com/kb/2990804)!  S Azure Cloud Services nebo Azure Websites stává hračkou díky přidružení vlastního názvu domény a zabezpečení komunikace (SSL).
> 
> 

## <a name="understand-cname-and-a-records"></a>Vysvětlení záznamy CNAME a A
Záznam CNAME (nebo záznamů aliasů) a záznamy A oba vám umožní přidružit určitý server názvu domény (nebo služby v tomto případě) ale fungují různě. Nejsou zde také některé důležité informace při použití záznamy A službou Azure Cloud services, které byste měli zvážit před rozhodování o tom, které chcete použít.

### <a name="cname-or-alias-record"></a>Záznam CNAME nebo Alias
Záznam CNAME, který se mapuje *konkrétní* domény, například **contoso.com** nebo **www.contoso.com**, na název domény canonical. V takovém případě je název domény canonical **[myapp] .cloudapp .net** aplikací hostovaných v Azure název domény. Po vytvoření záznamu CNAME vytvoří alias pro **[myapp] .cloudapp .net**. Záznam CNAME se přeloží IP adresu vašeho **[myapp] .cloudapp .net** služby automaticky, takže pokud se změní IP adresa cloudové služby, není nutné provádět žádnou akci.

> [!NOTE]
> Některé domény registrátorů umožňují pouze pro mapování subdomény, při použití záznam CNAME, třeba www.contoso.com a nikoli kořenový názvy, třeba contoso.com. Další informace o záznamech CNAME najdete v tématu v dokumentaci od vašeho registrátora [Wikipedia položka záznamu CNAME](http://en.wikipedia.org/wiki/CNAME_record), nebo [názvy domén IETF – implementace a specifikace](http://tools.ietf.org/html/rfc1035) dokumentu.
> 
> 

### <a name="a-record"></a>Záznam
*A* záznam mapuje doménu, například **contoso.com** nebo **www.contoso.com**, *nebo zástupné doméně* například  **\*. contoso.com**, IP adresu. V případě služby Azure, cloudové virtuální IP adresu služby. Hlavní výhodou služby záznam nad záznam CNAME, který je, že můžete mít jednu položku, která používá zástupný znak, jako například \* **. contoso.com**, který bude zpracovávat požadavky pro několik subdomén, jako  **mail.contoso.com**, **login.contoso.com**, nebo **www.contso.com**.

> [!NOTE]
> Protože záznam je namapována na statickou IP adresu, nedokáže automaticky vyřešit změny na IP adresu vaší cloudové služby. IP adresu používanou službou cloudové služby je přidělen při prvním nasazení do prázdné přihrádky (buď provozního nebo testovacího.) Pokud odstraníte nasazení pro slot, IP adresa se uvolní Azure a všechna budoucí nasazení do slotu může dostat novou IP adresu.
> 
> Pohodlná IP adresu zadaný slot pro nasazení (provozního nebo testovacího) je trvalé při prohození mezi přípravě a nasazení v produkčním prostředí nebo provedení místního upgradu existujícího nasazení. Další informace o provedení těchto akcí najdete v tématu [Správa cloudových služeb](cloud-services-how-to-manage-portal.md).
> 
> 

## <a name="add-a-cname-record-for-your-custom-domain"></a>Přidejte záznam CNAME pro vlastní doménu
Vytvořte záznam CNAME, musí přidáte nový záznam v tabulce DNS pro vaši vlastní doménu pomocí nástrojů podle vašeho registrátora. Každý Registrátor má podobné, ale mírně odlišné metodu pro určení záznam CNAME, ale koncepty jsou stejné.

1. Použijte jednu z těchto metod najít **. cloudapp.net** název domény, které jsou přiřazeny ke cloudové službě.
   
   * Přihlaste se k [Azure Portal], vyberte cloudovou službu, podívejte se na **Essentials** části a pak vyhledejte **adresa URL webu** položka.
     
       ![rychlý přehled část zobrazuje adresu URL webu][csurl]
     
       **OR**
   * Instalace a konfigurace [prostředí Azure Powershell](/powershell/azure/overview)a potom použijte následující příkaz:
     
       ```powershell
       Get-AzureDeployment -ServiceName yourservicename | Select Url
       ```
     
     Uložte název domény používaný v adrese URL vrácené některé z metod, protože ji budete potřebovat při vytváření záznamu CNAME.
2. Přihlaste se k webu vašeho registrátora DNS a přejděte na stránku pro správu DNS. Vyhledejte odkazy nebo oblasti webu označené jako **název domény**, **DNS**, nebo **Name Server Management**.
3. Nyní najdete, kde můžete vybrat nebo zadat CNAME pro. Bude pravděpodobně nutné vyberte typ záznamu rozevíracím seznamu, nebo přejděte na stránku rozšířeného nastavení. Je vhodné vyhledat slova **CNAME**, **Alias**, nebo **subdomény**.
4. Musíte taky zadat doménu nebo subdoménu alias pro záznam CNAME, jako například **www** Pokud budete chtít vytvořit alias **www.customdomain.com**. Pokud chcete vytvořit alias pro kořenovou doménu, může být uveden jako "**@**" symbol v nástrojích DNS vašeho registrátora.
5. Poté, je nutné zadat název hostitele canonical, které je vaše aplikace **cloudapp.net** domény v tomto případě.

Například následující záznam CNAME předává veškerý provoz z **www.contoso.com** k **contoso.cloudapp.net**, vlastní název domény nasazené aplikace:

| Název alias/hostitele nebo subdomény | Canonical domény |
| --- | --- |
| www |contoso.cloudapp.NET |

> [!NOTE]
> Návštěvník z **www.contoso.com** nikdy nezobrazí true hostitele (contoso.cloudapp.net), takže proces přesměrování je viditelná pro koncového uživatele.
> 
> Výše uvedený příklad platí jenom pro přenosy **www** subdomény. Vzhledem k tomu, že se záznamy CNAME nemůže použít zástupné znaky, je potřeba vytvořit jeden CNAME pro každou domény nebo subdomény. Pokud chcete směrovat provoz z subdomén, třeba *. contoso.com, na vaši adresu cloudapp.net můžete nakonfigurovat **adresu URL pro přesměrování** nebo **předat adresu URL** záznam v nastavení DNS, nebo vytvořit záznam.
> 
> 

## <a name="add-an-a-record-for-your-custom-domain"></a>Přidat záznam pro vaši vlastní doménu
Chcete-li vytvořit záznam A, je nutné nejprve vyhledat virtuální IP adresu vaší cloudové služby. Potom přidejte nový záznam v tabulce DNS pro vaši vlastní doménu pomocí nástroje poskytované subsystémem svého doménového registrátora. Každý Registrátor má podobné, ale mírně odlišné metodu pro určení záznam A, ale koncepty jsou stejné.

1. Použijte jednu z následujících metod k získání IP adresy cloudové služby.
   
   * Přihlaste se k [Azure Portal], vyberte cloudovou službu, podívejte se na **Essentials** části a pak vyhledejte **veřejné IP adresy** položka.
     
       ![rychlý přehled části zobrazující virtuální IP adresy][vip]
     
       **OR**
   * Instalace a konfigurace [prostředí Azure Powershell](/powershell/azure/overview)a potom použijte následující příkaz:
     
       ```powershell
       get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
       ```
     
     IP adresu, uložte, protože ji budete potřebovat při vytváření záznamu.
2. Přihlaste se k webu vašeho registrátora DNS a přejděte na stránku pro správu DNS. Vyhledejte odkazy nebo oblasti webu označené jako **název domény**, **DNS**, nebo **Name Server Management**.
3. Nyní najdete, kde můžete vybrat nebo zadat záznamu. Bude pravděpodobně nutné vyberte typ záznamu rozevíracím seznamu, nebo přejděte na stránku rozšířeného nastavení.
4. Vyberte nebo zadejte doménu nebo subdoménu, která bude používat tento záznam. Vyberte například **www** Pokud budete chtít vytvořit alias **www.customdomain.com**. Pokud chcete vytvořit položku zástupných znaků pro všechny subdomény, zadejte "***". To bude zahrnovat všechny subdomény, jako **mail.customdomain.com**, **login.customdomain.com**, a **www.customdomain.com**.
   
    Pokud chcete vytvořit záznam A pro kořenovou doménu, může být uveden jako "**@**" symbol v nástrojích DNS vašeho registrátora.
5. Zadejte IP adresu vaší cloudové služby v zadané pole. To spojí zadaná doména používaných pro záznam A IP adresou vašeho nasazení cloudové služby.

Například následující záznam předává veškerý provoz z **contoso.com** k **137.135.70.239**, IP adresu nasazené aplikace:

| Název hostitele nebo subdomény | IP adresa |
| --- | --- |
| \@ |137.135.70.239 |

Tento příklad ukazuje vytvoření záznamu A pro kořenovou doménu. Pokud chcete vytvořit záznam zástupných znaků pro všechny subdomény, zadali byste: *** "jako subdomény.

> [!WARNING]
> IP adresy v Azure jsou ve výchozím nastavení dynamické. Budete pravděpodobně chtít použít [vyhrazená IP adresa](../virtual-network/virtual-networks-reserved-public-ip.md) zajistit, že vaše IP adresa se nezmění.
> 
> 

## <a name="next-steps"></a>Další postup
* [Jak spravovat Cloud Services](cloud-services-how-to-manage-portal.md)
* [Postup mapování obsahu CDN do vlastní domény](../cdn/cdn-map-content-to-custom-domain.md)
* [Obecná konfigurace cloudové služby](cloud-services-how-to-configure-portal.md).
* Zjistěte, jak [nasadit cloudovou službu](cloud-services-how-to-create-deploy-portal.md).
* Konfigurace [certifikáty ssl](cloud-services-configure-ssl-certificate-portal.md).

[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: cloud-services-how-to-manage-portal.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[Azure Portal]: https://portal.azure.com
[vip]: ./media/cloud-services-custom-domain-name-portal/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name-portal/csurl.png

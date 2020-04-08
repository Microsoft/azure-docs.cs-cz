---
title: Konfigurace vlastního názvu domény v cloudových službách | Dokumenty společnosti Microsoft
description: Zjistěte, jak vystavit aplikaci nebo data Azure na internet ve vlastní doméně konfigurací nastavení DNS.  Tyto příklady používají portál Azure.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: tagore
ms.openlocfilehash: e764e6a474b9843d43f9e8af9cf3b6a8ddf37189
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811643"
---
# <a name="configuring-a-custom-domain-name-for-an-azure-cloud-service"></a>Konfigurace vlastního názvu domény pro cloudovou službu Azure
Když vytvoříte cloudovou službu, Azure ji přiřadí k subdoméně **cloudapp.net**. Pokud má například cloudová služba název "contoso", budou mít uživatelé `http://contoso.cloudapp.net`přístup k vaší aplikaci na adrese URL, jako je . Azure také přiřadí virtuální IP adresu.

Aplikaci však můžete také vystavit na vlastní název domény, například **contoso.com**. Tento článek vysvětluje, jak rezervovat nebo nakonfigurovat vlastní název domény pro webové role cloudové služby.

Už chápete, co jsou cname a a záznamy? [Přejít přes vysvětlení](#add-a-cname-record-for-your-custom-domain).

> [!NOTE]
> Postupy v této úloze platí pro Cloudové služby Azure. V tématu App Services [najdete v tématu Mapování existujícího vlastního názvu DNS na Azure Web Apps](../app-service/app-service-web-tutorial-custom-domain.md). Účty úložiště najdete [v tématu Konfigurace vlastního názvu domény pro koncový bod úložiště objektů blob Azure](../storage/blobs/storage-custom-domain-name.md).
> 
> 

<p/>

> [!TIP]
> Rychlejší práce – použijte [nový návod s průvodcem azure!](https://support.microsoft.com/kb/2990804)  To dělá spojení vlastní název domény a zabezpečení komunikace (TLS) s Azure Cloud Services nebo Azure Weby přichycení.
> 
> 

## <a name="understand-cname-and-a-records"></a>Principy záznamů CNAME a A
CNAME (nebo alias záznamy) a A záznamy umožňují přidružit název domény s konkrétním serverem (nebo službou v tomto případě), ale fungují jinak. Existují také některé konkrétní aspekty při použití záznamů A se službami Azure Cloud, které byste měli zvážit před rozhodnutím, které chcete použít.

### <a name="cname-or-alias-record"></a>Záznam CNAME nebo Alias
Záznam CNAME mapuje *určitou* doménu, například **contoso.com** nebo **www\.contoso.com**, na kanonický název domény. V takovém případě je název kanonické domény **název domény [myapp].cloudapp.net** vaší hostované aplikace Azure. Po vytvoření cname vytvoří alias pro **[myapp].cloudapp.net**. Položka CNAME se automaticky přetání na IP adresu vaší služby **[myapp].cloudapp.net,** takže pokud se změní IP adresa cloudové služby, nemusíte podnikat žádnou akci.

> [!NOTE]
> Někteří registrátoři domény umožňují mapovat subdomény pouze při použití záznamu\.CNAME, například www contoso.com, a nikoli kořenových názvů, například contoso.com. Další informace o záznamech CNAME naleznete v dokumentaci poskytnuté registrátorem, [v položce Wikipedie v záznamu CNAME](https://en.wikipedia.org/wiki/CNAME_record)nebo v dokumentu [IETF Domain Names - Implementation and Specification.](https://tools.ietf.org/html/rfc1035)

### <a name="a-record"></a>Záznam
Záznam *A* mapuje doménu, **například contoso.com** nebo www **\.contoso.com**, nebo *doménu se zástupnými symboly,* například ** \*.contoso.com**, na adresu IP. V případě cloudové služby Azure virtuální IP služby. Hlavní výhodou záznamu A nad záznamem CNAME je tedy to, že můžete mít \*jednu položku, která používá zástupný znak, například **.contoso.com**, který by zpracovával požadavky na více subdomén, jako je **mail.contoso.com**, **login.contoso.com**nebo **www\.contso.com**.

> [!NOTE]
> Vzhledem k tomu, že záznam A je mapován na statickou ADRESU IP, nemůže automaticky vyřešit změny IP adresy cloudové služby. IP adresa používaná cloudovou službou je přidělena při prvním nasazení do prázdného slotu (produkčního nebo pracovního). Pokud odstraníte nasazení pro patici, IP adresa je uvolněna Azure a jakékoli budoucí nasazení do patice může být poskytnuta nová IP adresa.
> 
> Pohodlně IP adresa daného slotu nasazení (produkční nebo pracovní) je zachována při přepnutí mezi pracovní a produkční nasazení nebo provádění inovace na místě existujícího nasazení. Další informace o provádění těchto akcí naleznete v tématu [Správa cloudových služeb](cloud-services-how-to-manage-portal.md).
> 
> 

## <a name="add-a-cname-record-for-your-custom-domain"></a>Přidání záznamu CNAME pro vlastní doménu
Chcete-li vytvořit záznam CNAME, musíte přidat novou položku do tabulky DNS pro vlastní doménu pomocí nástrojů poskytnutých registrátorem. Každý registrátor má podobnou, ale mírně odlišnou metodu určení záznamu CNAME, ale koncepty jsou stejné.

1. Pomocí jedné z těchto metod vyhledejte název domény **.cloudapp.net** přiřazený vaší cloudové službě.

   * Přihlaste se na [portál Azure], vyberte cloudovou službu, podívejte se do části **Přehled** a najděte položku **URL webu.**

       ![Stručný přehled sekce zobrazující adresu URL webu][csurl]

       **Nebo**
   * Nainstalujte a nakonfigurujte [Azure Powershell](/powershell/azure/overview)a pak použijte následující příkaz:

       ```powershell
       Get-AzureDeployment -ServiceName yourservicename | Select Url
       ```

     Uložte název domény použitý v adrese URL vrácené oběma způsoby, protože jej budete potřebovat při vytváření záznamu CNAME.
2. Přihlaste se na web registrátora DNS a přejděte na stránku pro správu služby DNS. Vyhledejte odkazy nebo oblasti webu označené jako **Název domény**, **DNS**nebo **Správa názvového serveru**.
3. Nyní zjistěte, kde si můžete vybrat nebo zadat CNAME. Možná budete muset vybrat typ záznamu z rozevíracího souboru nebo přejít na stránku s upřesňujícím nastavením. Měli byste hledat slova **CNAME**, **Alias**nebo **Subdomény**.
4. Musíte také zadat alias domény nebo subdomény pro CNAME, například **www,** pokud chcete vytvořit alias pro **www\.customdomain.com**. Chcete-li vytvořit alias pro kořenovou doménu, může**\@** být uveden jako symbol " v nástrojích DNS registrátora.
5. Potom je nutné zadat název kanonického hostitele, což je **cloudapp.net** doména vaší aplikace v tomto případě.

Například následující záznam CNAME předává veškerý provoz z **\.www contoso.com** do **contoso.cloudapp.net**, vlastní název domény nasazené aplikace:

| Alias/název hostitele/subdoména | Kanonické domény |
| --- | --- |
| www |contoso.cloudapp.net |

> [!NOTE]
> Návštěvník **www\.contoso.com** nikdy neuvidí skutečného hostitele (contoso.cloudapp.net), takže proces předávání je pro koncového uživatele neviditelný.
> 
> Výše uvedený příklad se vztahuje pouze na provoz v **subdoméně www.** Vzhledem k tomu, že nelze použít zástupné znaky se záznamy CNAME, je nutné vytvořit jeden CNAME pro každou doménu nebo subdoménu. Pokud chcete směrovat provoz ze subdomén, například *.contoso.com, na vaši cloudapp.net adresu, můžete v nastavení DNS nakonfigurovat položku **Přesměrování adresy URL** nebo **Předávací adresa URL** nebo vytvořit záznam A.

## <a name="add-an-a-record-for-your-custom-domain"></a>Přidání záznamu A pro vlastní doménu
Chcete-li vytvořit záznam A, musíte nejprve najít virtuální IP adresu vaší cloudové služby. Potom přidejte novou položku do tabulky DNS pro vlastní doménu pomocí nástrojů poskytnutých registrátorem. Každý registrátor má podobnou, ale mírně odlišnou metodu určení záznamu A, ale koncepty jsou stejné.

1. Pomocí jedné z následujících metod získáte IP adresu cloudové služby.

   * Přihlaste se na [portál Azure], vyberte cloudovou službu, podívejte se do části **Přehled** a najděte položku Veřejné **IP adresy.**

       ![rychlá pohled sekce zobrazující VIP][vip]

       **Nebo**
   * Nainstalujte a nakonfigurujte [Azure Powershell](/powershell/azure/overview)a pak použijte následující příkaz:

       ```powershell
       get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
       ```

     Uložte ip adresu, protože ji budete potřebovat při vytváření záznamu A.
2. Přihlaste se na web registrátora DNS a přejděte na stránku pro správu služby DNS. Vyhledejte odkazy nebo oblasti webu označené jako **Název domény**, **DNS**nebo **Správa názvového serveru**.
3. Nyní zjistěte, kde můžete vybrat nebo zadat záznam. Možná budete muset vybrat typ záznamu z rozevíracího souboru nebo přejít na stránku s upřesňujícím nastavením.
4. Vyberte nebo zadejte doménu nebo subdoménu, která bude tento záznam A používat. Vyberte například **www,** pokud chcete vytvořit alias pro **www\.customdomain.com**. Pokud chcete vytvořit položku se zástupnými symboly pro všechny subdomény, zadejte '*****'. To se bude týkat všech subdomén, jako jsou **mail.customdomain.com**, **login.customdomain.com**a **www\.customdomain.com**.

    Chcete-li vytvořit záznam A pro kořenovou doménu, může**\@** být uveden jako symbol " v nástrojích DNS registrátora.
5. Do poskytnutého pole zadejte IP adresu cloudové služby. Tím přidružíte položku domény použitou v záznamu A k IP adrese nasazení cloudové služby.

Například následující záznam A předává veškerý provoz z **contoso.com** na **137.135.70.239**, IP adresu nasazené aplikace:

| Název hostitele/subdoména | IP adresa |
| --- | --- |
| \@ |137.135.70.239 |

Tento příklad ukazuje vytvoření záznamu A pro kořenovou doménu. Pokud chcete vytvořit položku se zástupnými symboly, která by pokrývala všechny subdomény, zadejte jako subdoménu "*****".

> [!WARNING]
> IP adresy v Azure jsou ve výchozím nastavení dynamické. Pravděpodobně budete chtít použít [vyhrazenou ADRESU IP,](../virtual-network/virtual-networks-reserved-public-ip.md) abyste zajistili, že se vaše IP adresa nezmění.
> 
> 

## <a name="next-steps"></a>Další kroky
* [Jak spravovat cloudové služby](cloud-services-how-to-manage-portal.md)
* [Postup mapování obsahu CDN do vlastní domény](../cdn/cdn-map-content-to-custom-domain.md)
* [Obecná konfigurace cloudové služby](cloud-services-how-to-configure-portal.md).
* Přečtěte si, jak [nasadit cloudovou službu](cloud-services-how-to-create-deploy-portal.md).
* Konfigurace [certifikátů TLS/SSL](cloud-services-configure-ssl-certificate-portal.md).

[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: cloud-services-how-to-manage-portal.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[portál Azure]: https://portal.azure.com
[vip]: ./media/cloud-services-custom-domain-name-portal/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name-portal/csurl.png




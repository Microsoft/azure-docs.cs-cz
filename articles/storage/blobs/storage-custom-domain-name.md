---
title: Konfigurace vlastního názvu domény pro svůj účet úložiště Azure | Microsoft Docs
description: Pomocí Azure Portal namapujte vlastní kanonický název (CNAME) na úložiště objektů BLOB nebo webový koncový bod v účtu úložiště Azure.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: ae809dd4841d6df8caabebfaded263b52f386834
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68845041"
---
# <a name="configure-a-custom-domain-name-for-your-azure-storage-account"></a>Konfigurace vlastního názvu domény pro účet služby Azure Storage

Můžete nakonfigurovat vlastní doménu pro přístup k datům objektů BLOB v účtu úložiště Azure. Výchozím koncovým bodem pro úložiště objektů BLOB v Azure je  *\<název účtu úložiště >. blob. Core. Windows. NET*. Můžete také použít koncový bod webu, který je vygenerován jako součást [funkce static](storage-blob-static-website.md)websites. Pokud namapujete vlastní doménu a subdoménu (například *Webová\.contoso.com*) na koncový bod objektu BLOB nebo webový koncový bod vašeho účtu úložiště, můžou uživatelé tuto doménu použít pro přístup k datům objektů BLOB v účtu úložiště.

> [!IMPORTANT]
> Azure Storage ještě nativně nepodporuje protokol HTTPS s vlastními doménami. V současné době můžete [pomocí Azure CDN přistupovat k objektům blob pomocí vlastních domén přes protokol HTTPS](storage-https-custom-domain-cdn.md).
> 
> 
> [!NOTE]
> Účty úložiště aktuálně podporují jenom jeden vlastní název domény pro každý účet. Vlastní název domény se nedá namapovat na koncové body služby Web a BLOB Service.
> 
> [!NOTE]
> Mapování funguje pouze pro subdomény (např. www\.contoso.com). Pokud chcete, aby byl váš webový koncový bod dostupný v kořenové doméně (např. contoso.com), musíte [použít Azure CDN s vlastními doménami](storage-https-custom-domain-cdn.md) .

Následující tabulka obsahuje několik ukázkových adres URL pro data objektů BLOB nacházející se v účtu úložiště s názvem *mystorageaccount*. Vlastní subdoménou, která je zaregistrovaná pro účet úložiště, je *Webová\.contoso.com*:

| Typ prostředku | Výchozí adresa URL | Adresa URL vlastní domény |
| --- | --- | --- |
| Účet úložiště | http:\//mystorageaccount.blob.core.windows.net | http:\//www.contoso.com |
| Blob |http:\//mystorageaccount.blob.core.windows.net/mycontainer/myblob | http:\//www.contoso.com/mycontainer/myblob |
| Kořenový kontejner | http:\//mystorageaccount.blob.Core.Windows.NET/myblob nebo http:\//mystorageaccount.blob.Core.Windows.NET/$root/myblob | http:\//www.contoso.com/myblob nebo http:\//www.contoso.com/$root/myblob |
| Web |  http:\//mystorageaccount. [ Zone]. Web. Core. Windows. NET/$Web/[indexdoc] nebo http:\//mystorageaccount. [ Zone]. Web. Core. Windows. NET/[indexdoc] nebo http:\//mystorageaccount. [ Zone]. Web. Core. Windows. NET/$Web nebo http:\//mystorageaccount. [ Zone]. Web. Core. Windows. NET/ | http:\//www.contoso.com/$web nebo http:\//www.contoso.com/nebo http:\//www.contoso.com/$web/[indexdoc] nebo http:\//www.contoso.com/[indexdoc] |

> [!NOTE]  
> Jak je znázorněno v následujících částech, všechny příklady pro koncový bod služby BLOB Service platí také pro koncový bod webové služby.

## <a name="direct-vs-intermediary-cname-mapping"></a>Mapování přímého a zprostředkujícího CNAME

Vlastní doménu s poddoménou (např.\.contoso.com) můžete nasměrovat na koncový bod objektu BLOB pro účet úložiště jedním ze dvou způsobů: 
* Použijte přímé mapování CNAME.
* Použijte subdoménu zprostředkujícího *asverify* .

### <a name="direct-cname-mapping"></a>Mapování přímého záznamu CNAME

První a nejjednodušší metodou je vytvořit záznam kanonického názvu (CNAME), který mapuje vlastní doménu a subdoménu přímo na koncový bod objektu BLOB. Záznam CNAME je funkce DNS (Domain Name System), která mapuje zdrojovou doménu na cílovou doménu. V našem příkladu je zdrojová doména vlastní doménou a subdoménou (například*Webová\.contoso.com*). Cílová doména je vaším koncovým bodem služby BLOB Service (například*mystorageaccount.blob.Core.Windows.NET*).

Přímý způsob je popsaný v části "Registrace vlastní domény".

### <a name="intermediary-mapping-with-asverify"></a>Mapování zprostředkujících pomocí *asverify*

Druhá metoda také používá záznamy CNAME. Aby se předešlo výpadkům, ale nejdřív využívá speciální subdoménu *asverify* , která je rozpoznaná v Azure.

Mapování vlastní domény na koncový bod objektu BLOB může způsobit krátkou dobu výpadků při registraci domény v [Azure Portal](https://portal.azure.com). Pokud doména aktuálně podporuje aplikaci se smlouvou o úrovni služeb (SLA), která vyžaduje žádné výpadky, použijte jako krok zprostředkující registraci poddoménu Azure *asverify* . Tento krok zajistí, že uživatelé budou mít přístup k vaší doméně, zatímco probíhá mapování DNS.

Zprostředkující metoda je popsána v části registrace vlastní domény pomocí subdomény *asverify* .

## <a name="register-a-custom-domain"></a>Registrace vlastní domény
Pokud chcete použít následující příkazy, Zaregistrujte doménu pomocí postupu v této části:
* Nejste si nerozhodli, že je doména krátce nedostupná pro vaše uživatele.
* Vaše vlastní doména aktuálně nehostuje aplikaci. 

K nakonfigurování vlastního názvu DNS pro úložiště objektů BLOB v Azure můžete použít Azure DNS. Další informace najdete v tématu popisujícím [použití Azure DNS k určení nastavení vlastní domény pro službu Azure](https://docs.microsoft.com/azure/dns/dns-custom-domain#blob-storage).

Pokud vaše vlastní doména aktuálně podporuje aplikaci, která nemůže mít nějaké výpadky, použijte postup v části registrace vlastní domény pomocí subdomény *asverify* .

Pokud chcete nakonfigurovat vlastní název domény, vytvořte v DNS nový záznam CNAME. Záznam CNAME Určuje alias pro název domény. V našem příkladu mapuje adresu vlastní domény do koncového bodu služby Blob Storage účtu úložiště.

Obvykle můžete spravovat nastavení DNS vaší domény na webu vašeho doménového registrátora. Každý registrátor má podobnou, ale mírně odlišnou metodu určení záznamu CNAME, ale koncept je stejný. Vzhledem k tomu, že některé balíčky základní registrace domény nenabízejí konfiguraci DNS, možná budete muset před vytvořením záznamu CNAME upgradovat registrační balíček domény.

1. V [Azure Portal](https://portal.azure.com)přejít na účet úložiště.

1. V podokně nabídek v části **BLOB Service**vyberte **vlastní doména**.  
   Otevře se podokno **vlastní doména** .

1. Přihlaste se k webu vašeho doménového registrátora a pak na stránce pro správu DNS se pokuste přejít na stránku.  
   Stránku můžete najít v části s názvem **název domény**, **DNS**nebo **Správa názvového serveru**.

1. Najděte část pro správu záznamů CNAME.  
   Možná budete muset přejít na stránku pokročilá nastavení a vyhledat **CNAME**, **alias**nebo subdomény.

1. Vytvořte nový záznam CNAME, zadejte alias subdomény, jako je například **www** nebo **fotografie** (je třeba použít subdomény, kořenové domény nejsou podporovány) a pak zadejte název hostitele.  
   Název hostitele je váš koncový bod služby BLOB Service. Jeho formát je  *\<mystorageaccount >. blob. Core. Windows. NET*, kde *mystorageaccount* je název vašeho účtu úložiště. Název hostitele, který se má použít, se zobrazí v položce #1 podokna **vlastní doména** v [Azure Portal](https://portal.azure.com). 

1. V podokně **vlastní doména** zadejte do textového pole název vlastní domény, včetně subdomény.  
   Pokud je vaše doména například *contoso.com* a alias subdomény je *www*, zadejte **webové\.contoso.com**. Pokud je vaše poddoména *fotografie*, zadejte **photos.contoso.com**.

1. Pokud chcete zaregistrovat vlastní doménu, vyberte **Uložit**.  
   Pokud je registrace úspěšná, portál vás upozorní, že váš účet úložiště se úspěšně aktualizoval.

Po rozšíření nového záznamu CNAME prostřednictvím DNS budou mít uživatelé potřebná oprávnění, aby mohli zobrazit data objektů BLOB pomocí vlastní domény.

## <a name="register-a-custom-domain-by-using-the-asverify-subdomain"></a>Registrace vlastní domény pomocí subdomény *asverify*
Pokud vaše vlastní doména aktuálně podporuje aplikaci se smlouvou SLA, která vyžaduje, aby nedocházelo k výpadkům, zaregistrujte vlastní doménu pomocí postupu v této části. Vytvořením záznamu CNAME, který odkazuje na *asverify\< . > subdomény customdomain\<>* do *asverify.\< storageaccount >. blob. Core. Windows. NET*, můžete doménu předem zaregistrovat v Azure. Pak můžete vytvořit druhý záznam CNAME, který bude ukazovat z  *\<> subdomény\< . customdomain >* do  *\<storageaccount >. blob. Core. Windows. NET*a pak se provoz do vaší vlastní domény přesměruje na koncový bod objektu BLOB.

Subdoménou *asverify* je speciální subdoména rozpoznaná v Azure. Tím, že *asverify* předčekání na vlastní subdoménu, umožníte Azure rozpoznat vlastní doménu, aniž byste museli upravovat záznam DNS pro doménu. Když změníte záznam DNS pro doménu, namapuje se na koncový bod objektu BLOB bez výpadků.

1. V [Azure Portal](https://portal.azure.com)přejít na účet úložiště.

1. V podokně nabídek v části **BLOB Service**vyberte **vlastní doména**.  
   Otevře se podokno **vlastní doména** .

1. Přihlaste se k webu svého poskytovatele DNS a pak na stránce pro správu DNS se pokuste přejít na stránku.  
   Stránku můžete najít v části s názvem **název domény**, **DNS**nebo **Správa názvového serveru**.

1. Najděte část pro správu záznamů CNAME.  
   Možná budete muset přejít na stránku pokročilá nastavení a vyhledat **CNAME**, **alias**nebo subdomény.

1. Vytvořte nový záznam CNAME, zadejte alias subdomény, který obsahuje subdoménu *asverify* , jako je například **asverify. www** nebo **asverify. photos**, a pak zadejte název hostitele.  
   Název hostitele je váš koncový bod služby BLOB Service. Formát je *asverify.\< mystorageaccount >. blob. Core. Windows. NET*, kde *mystorageaccount* je název vašeho účtu úložiště. Název hostitele, který se má použít, se zobrazí v položce #2 podokna *vlastní doména* v [Azure Portal](https://portal.azure.com).

1. V podokně **vlastní doména** zadejte do textového pole název vlastní domény, včetně subdomény.  
   Nezahrnujte *asverify*. Pokud je vaše doména například *contoso.com* a alias subdomény je *www*, zadejte **webové\.contoso.com**. Pokud je vaše poddoména *fotografie*, zadejte **photos.contoso.com**.

1. Zaškrtněte políčko **použít nepřímé ověřování CNAME** .

1. Pokud chcete zaregistrovat vlastní doménu, vyberte **Uložit**.  
   Pokud je registrace úspěšná, portál vás upozorní, že váš účet úložiště se úspěšně aktualizoval. Vaše vlastní doména byla ověřena v Azure, ale provoz do vaší domény ještě není směrován do svého účtu úložiště.

1. Vraťte se na web poskytovatele DNS a pak vytvořte další záznam CNAME, který mapuje subdoménu na koncový bod služby BLOB Service.  
   Zadejte například subdoménu jako *www* nebo Photos (bez *asverify*) a zadejte název hostitele jako  *\<mystorageaccount >. blob. Core. Windows. NET*, kde *mystorageaccount* je název vašeho účet úložiště. V tomto kroku je registrace vlastní domény dokončená.

1. Nakonec můžete odstranit nově vytvořený záznam CNAME, který obsahuje subdoménu *asverify* , která se vyžadovala jenom jako zprostředkující krok.

Po rozšíření nového záznamu CNAME prostřednictvím DNS budou mít uživatelé potřebná oprávnění, aby mohli zobrazit data objektů BLOB pomocí vlastní domény.

## <a name="test-your-custom-domain"></a>Testování vlastní domény

Pokud chcete potvrdit, že je vaše vlastní doména namapovaná na koncový bod služby BLOB Service, vytvořte v rámci svého účtu úložiště objekt BLOB ve veřejném kontejneru. Pak ve webovém prohlížeči přejděte k objektu BLOB pomocí identifikátoru URI v následujícím formátu:`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Pokud například chcete získat přístup k webovému formuláři v kontejneru *myforms* ve vlastní subdoméně *photos.contoso.com* , můžete použít následující identifikátor URI:`http://photos.contoso.com/myforms/applicationform.htm`

## <a name="deregister-a-custom-domain"></a>Zrušení registrace vlastní domény

Pokud chcete zrušit registraci vlastní domény pro koncový bod služby Blob Storage, použijte jeden z následujících postupů.

### <a name="azure-portal"></a>portál Azure

Pokud chcete odebrat vlastní nastavení domény, udělejte toto:

1. V [Azure Portal](https://portal.azure.com)přejít na účet úložiště.

1. V podokně nabídek v části **BLOB Service**vyberte **vlastní doména**.  
   Otevře se podokno **vlastní doména** .

1. Vymažte obsah textového pole, které obsahuje vlastní název domény.

1. Vyberte tlačítko **Uložit**.

Po úspěšném odebrání vlastní domény se zobrazí oznámení na portálu, že váš účet úložiště se úspěšně aktualizoval.

### <a name="azure-cli"></a>Azure CLI

Pokud chcete odebrat vlastní registraci domény, použijte příkaz [AZ Storage Account Update](https://docs.microsoft.com/cli/azure/storage/account) CLI a pak zadejte prázdný řetězec (`""`) pro `--custom-domain` hodnotu argumentu.

* Formát příkazu:

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* Příklad příkazu:

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pokud chcete odebrat vlastní registraci domény, použijte rutinu [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) prostředí PowerShell a pak zadejte prázdný řetězec (`""`) pro `-CustomDomainName` hodnotu argumentu.

* Formát příkazu:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Příklad příkazu:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```

## <a name="next-steps"></a>Další postup
* [Mapování vlastní domény na koncový bod Azure Content Delivery Network (CDN)](../../cdn/cdn-map-content-to-custom-domain.md)
* [Použití Azure CDN k přístupu k objektům blob pomocí vlastních domén přes protokol HTTPS](storage-https-custom-domain-cdn.md)
* [Hostování statických webů ve službě Azure Blob Storage (Preview)](storage-blob-static-website.md)

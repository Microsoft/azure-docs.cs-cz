---
title: Konfigurace vlastního názvu domény pro váš účet úložiště Azure | Dokumentace Microsoftu
description: Mapování vlastních kanonického názvu (CNAME) do úložiště nebo webový koncový bod služby Blob v účtu služby Azure storage pomocí webu Azure portal.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/26/2018
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: f0fe4bef436576bec90d1d770d262c2c22d280a3
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55694580"
---
# <a name="configure-a-custom-domain-name-for-your-azure-storage-account"></a>Konfigurace vlastního názvu domény pro váš účet úložiště Azure

Můžete nakonfigurovat vlastní doménu pro přístup k datům objektu blob v účtu úložiště Azure. Výchozí koncový bod pro úložiště objektů Blob v Azure je  *\<název účtu úložiště >. blob.core.windows.net*. Můžete také použít webový koncový bod, který je generován jako součást [funkce statických webů (preview)](storage-blob-static-website.md). Pokud například mapování vlastní domény a subdomény, *www.contoso.com*, ke koncovému bodu objektu blob nebo web pro váš účet úložiště, můžou uživatelé používala tuto doménu pro přístup k datům objektu blob v účtu úložiště.

> [!IMPORTANT]
> Azure Storage zatím nativně nepodporuje protokol HTTPS s použitím vlastních domén. Aktuálně můžete [použití Azure CDN přístup k objektům BLOB s použitím vlastních domén přes protokol HTTPS](storage-https-custom-domain-cdn.md).
>

> [!NOTE]  
> Účty úložiště se aktuálně podporují jenom jeden vlastní název domény na jeden účet. Vlastní název domény nelze mapovat do koncových bodů služby web a objektů blob.

Následující tabulka ukazuje několik ukázky adresy URL pro data objektů blob, který se nachází v účtu úložiště s názvem *mystorageaccount*. Vlastní domény, který je registrovaný pro účet úložiště je *www.contoso.com*:

| Typ prostředku | Výchozí adresa URL | Adresa URL vlastní domény |
| --- | --- | --- | --- |
| Účet úložiště | http://mystorageaccount.blob.core.windows.net | http://www.contoso.com |
| Objekt blob |http://mystorageaccount.blob.core.windows.net/mycontainer/myblob | http://www.contoso.com/mycontainer/myblob |
| Kořenový kontejner | http://mystorageaccount.blob.core.windows.net/myblob nebo http://mystorageaccount.blob.core.windows.net/$root/myblob| http://www.contoso.com/myblob nebo http://www.contoso.com/$root/myblob |
| Web |  http://mystorageaccount. [zone].web.core.windows.net/$web/[indexdoc] nebo http://mystorageaccount. [ Zone].Web.Core.Windows.NET/[indexdoc] nebo http://mystorageaccount. [ Zone].Web.Core.Windows.NET/$Web nebo http://mystorageaccount. [ Zone].Web.Core.Windows.NET/ | http://www.contoso.com/$web nebo http://www.contoso.com/ nebo http://www.contoso.com/$web / [indexdoc] nebo http://www.contoso.com/[indexdoc] |

> [!NOTE]  
> Jak je znázorněno v následujících částech, pro koncový bod služby blob service ve všech příkladech platí také pro koncový bod webové služby.

## <a name="direct-vs-intermediary-domain-mapping"></a>Přímé porovnání zprostředkující domény mapování

Vlastní domény může odkazovat na koncový bod objektu blob pro účet úložiště v některém ze dvou způsobů: 
* Použijte přímé mapování CNAME.
* Použití *asverify* zprostředkující subdomény.

### <a name="direct-cname-mapping"></a>Přímé mapování CNAME

Metoda první a nejjednodušší, je vytvořit záznam kanonického názvu (CNAME), který mapuje vaši vlastní doménu a subdoménu přímo na koncový bod objektu blob. Záznam CNAME je funkce system (DNS) název domény, která mapuje zdrojovou doménu na cílovou doménu. V našem příkladu je ve zdrojové doméně své vlastní doménu a subdoménu (*www.contoso.com*, například). Cílová doména je koncový bod služby blob service (*mystorageaccount.blob.core.windows.net*, například).

Přímé metody je popsané v části "Zaregistrovat vlastní doménu".

### <a name="intermediary-mapping-with-asverify"></a>Zprostředkující mapování *asverify*

Druhá metoda používá také záznamy CNAME. Výpadky, ale nejprve využívá speciální subdomény *asverify* , který je rozpoznáván Azure.

Mapování vlastní domény do koncového bodu objektu blob může způsobit na krátkou dobu výpadku během registrace domény v [webu Azure portal](https://portal.azure.com). Pokud domény aktuálně podporuje aplikace s smlouvu o úrovni služeb (SLA), která nevyžaduje žádný výpadek, použijte Azure *asverify* subdoménu jako krok zprostředkující registrace. Tento krok zajistí, že uživatelé během mapování DNS přístup vaší domény.

Se věnuje metodě zprostředkující [registrace vlastní domény s použitím *asverify* subdoménu](#register-a-custom-domain-using-the-asverify-subdomain).

## <a name="register-a-custom-domain"></a>Registrace vlastní domény
Registrace domény pomocí postupu v této části, je-li použít následující příkazy:
* Jste unconcerned doména je krátce není k dispozici pro vaše uživatele.
* Vaše vlastní doména není aktuálně hostování aplikace. 

Azure DNS můžete použít ke konfiguraci vlastního názvu DNS pro úložiště objektů Blob v Azure. Další informace najdete v tématu popisujícím [použití Azure DNS k určení nastavení vlastní domény pro službu Azure](https://docs.microsoft.com/azure/dns/dns-custom-domain#blob-storage).

Pokud vaši vlastní doménu aktuálně podporuje aplikace, která nemůže mít žádné výpadky, použijte postup v [zaregistrovat se pomocí vlastní domény *asverify* subdomény](#register-a-custom-domain-using-the-asverify-subdomain).

Konfigurace vlastního názvu domény, vytvořte nový záznam CNAME v DNS. Určuje záznam CNAME, alias pro název domény. V našem příkladu mapuje adresu vaši vlastní doménu na koncový bod vašeho účtu úložiště Blob storage.

Obvykle můžete spravovat nastavení DNS pro vaši doménu na webu vašeho registrátora domény. Každý Registrátor má podobné, ale mírně odlišné metodu pro určení záznam CNAME, ale koncept je stejný. Protože některé balíčky doména pro základní registrace nenabízí konfigurace DNS, můžete potřebovat k upgradu vaší domény registrační balíček, než budete moct vytvořit záznam CNAME.

1. V [webu Azure portal](https://portal.azure.com), přejděte do svého účtu úložiště.

1. Na panelu nabídky klikněte v části **služby Blob Service**vyberte **vlastní domény**.  
   **Vlastní domény** se otevře podokno.

1. Přihlaste se k webu vašeho registrátora domény a pak přejděte na stránku pro správu DNS.  
   Můžete se setkat na stránce v části s názvem **název domény**, **DNS**, nebo **Name Server Management**.

1. Najděte část pro správu záznamů CNAME.  
   Budete muset přejít na stránku rozšířeného nastavení a Hledat **CNAME**, **Alias**, nebo **subdomény**.

1. Vytvoří nový záznam CNAME, zadejte subdoménu alias jako **www** nebo **fotografie**a potom zadejte název hostitele.  
   Je název hostitele vašeho koncového bodu služby objektů blob. Má formát  *\<mystorageaccount >. blob.core.windows.net*, kde *mystorageaccount* je název vašeho účtu úložiště. Název hostitele pro použití se zobrazí u položky #1 **vlastní domény** v podokně [webu Azure portal](https://portal.azure.com).

1. V **vlastní domény** podokně, v textovém poli zadejte název vlastní doménu včetně poddomény.  
   Například, pokud je vaše doména *contoso.com* a je váš alias subdoménu *www*, zadejte **www.contoso.com**. Pokud je vaše subdoménu *fotografie*, zadejte **photos.contoso.com**.

1. Chcete-li zaregistrovat ve vaší vlastní doméně, vyberte **Uložit**.  
   Pokud je úspěšné registraci, portál vás upozorní, že váš účet úložiště se úspěšně aktualizovala.

Po nový záznam CNAME se rozšíří prostřednictvím DNS, pokud mají vaši uživatelé příslušná oprávnění, se můžete zobrazit data objektů blob s použitím vlastní domény.

## <a name="register-a-custom-domain-by-using-the-asverify-subdomain"></a>Zaregistrovat se pomocí vlastní domény *asverify* subdomény
Pokud ve vaší vlastní doméně aktuálně podporuje aplikace se smlouvou SLA, která vyžaduje, aby existuje bez výpadku, zaregistrujte vaši vlastní doménu pomocí postupu v této části. Tím, že vytvoříte záznam CNAME, který se odkazuje z *asverify.\< subdoména >. \<chyby >* k *asverify.\< účet úložiště >. blob.core.windows.net*, můžete předem zaregistrovat vaši doménu v Azure. Potom můžete vytvořit druhý záznam CNAME, který se odkazuje z  *\<subdoména >.\< chyby >* k  *\<účet úložiště >. blob.core.windows.net*, a pak se provoz do vaší vlastní doméně směřuje na koncový bod služby blob.

*Asverify* subdoménu je speciální subdoména rozpoznána platformou Azure. Předponou v podobě *asverify* vlastní poddomény povolit Azure bez nutnosti upravit záznam DNS pro doménu rozpoznala vaši vlastní doménu. Při úpravě záznamu DNS pro doménu, se namapují na koncový bod služby blob došlo k výpadku.

1. V [webu Azure portal](https://portal.azure.com), přejděte do svého účtu úložiště.

1. Na panelu nabídky klikněte v části **služby Blob Service**vyberte **vlastní domény**.  
   **Vlastní domény** se otevře podokno.

1. Přihlaste se k webu vašeho poskytovatele DNS a potom přejděte na stránku pro správu DNS.  
   Můžete se setkat na stránce v části s názvem **název domény**, **DNS**, nebo **Name Server Management**.

1. Najděte část pro správu záznamů CNAME.  
   Budete muset přejít na stránku rozšířeného nastavení a Hledat **CNAME**, **Alias**, nebo **subdomény**.

1. Vytvoří nový záznam CNAME, zadejte subdoménu alias, který zahrnuje *asverify* subdomény, jako například **asverify.www** nebo **asverify.photos**a potom zadejte název hostitele.  
   Je název hostitele vašeho koncového bodu služby objektů blob. Má formát *asverify.\< mystorageaccount >. blob.core.windows.net*, kde *mystorageaccount* je název vašeho účtu úložiště. Název hostitele pro použití se zobrazí v bodu #2 *vlastní domény* v podokně [webu Azure portal](https://portal.azure.com).

1. V **vlastní domény** podokně, v textovém poli zadejte název vlastní doménu včetně poddomény.  
   Nezahrnují *asverify*. Například, pokud je vaše doména *contoso.com* a je váš alias subdoménu *www*, zadejte **www.contoso.com**. Pokud je vaše subdoménu *fotografie*, zadejte **photos.contoso.com**.

1. Vyberte **použít nepřímé ověření CNAME** zaškrtávací políčko.

1. Chcete-li zaregistrovat ve vaší vlastní doméně, vyberte **Uložit**.  
   Pokud je úspěšné registraci, portál vás upozorní, že váš účet úložiště se úspěšně aktualizovala. Vaše vlastní doména se ověřila pomocí Azure, ale provoz do domény není dosud směrovány do vašeho účtu úložiště.

1. Zpět na web poskytovatele DNS a potom vytvořit jiný záznam CNAME, který mapuje vaše subdomény na koncový bod služby blob service.  
   Například zadejte subdoménu jako *www* nebo *fotografie* (bez *asverify*) a zadejte název hostitele jako  *\<mystorageaccount >. blob.core.windows.net*, kde *mystorageaccount* je název vašeho účtu úložiště. S tímto krokem je dokončení registrace vlastní domény.

1. Nakonec můžete odstranit nově vytvořený záznam CNAME, který obsahuje *asverify* subdoménu, která byla požadována pouze jako zprostředkující kroky.

Po nový záznam CNAME se rozšíří prostřednictvím DNS, pokud mají vaši uživatelé příslušná oprávnění, se můžete zobrazit data objektů blob s použitím vlastní domény.

## <a name="test-your-custom-domain"></a>Testování vlastní domény

Pokud chcete potvrdit, že vaše vlastní doména se mapuje na koncový bod služby blob service, vytvoření objektu blob ve veřejném kontejneru v účtu úložiště. Ve webovém prohlížeči přístupu objektu blob s použitím identifikátoru URI v následujícím formátu: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Například pro přístup k webové formuláře v *myforms* kontejneru *photos.contoso.com* vlastní subdomény, můžete použít následující identifikátor URI: `http://photos.contoso.com/myforms/applicationform.htm`

## <a name="deregister-a-custom-domain"></a>Zrušení registrace vlastní domény

Zrušení registrace vlastní domény pro koncový bod služby Blob storage, použijte jednu z následujících postupů.

### <a name="azure-portal"></a>portál Azure

Pokud chcete odebrat nastavení vlastní domény, postupujte takto:

1. V [webu Azure portal](https://portal.azure.com), přejděte do svého účtu úložiště.

1. Na panelu nabídky klikněte v části **služby Blob Service**vyberte **vlastní domény**.  
   **Vlastní domény** se otevře podokno.

1. Vymazání obsahu textového pole, která obsahuje vlastní název domény.

1. Vyberte tlačítko **Uložit**.

Po vlastní domény byl úspěšně odebrán, zobrazí se oznámení na portálu účtu úložiště se úspěšně aktualizovala.

### <a name="azure-cli"></a>Azure CLI

K odebrání registrace vlastní domény, použijte [aktualizace účtu úložiště az](https://docs.microsoft.com/cli/azure/storage/account) rozhraní příkazového řádku příkaz a pak zadejte prázdný řetězec (`""`) pro `--custom-domain` hodnota argumentu.

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

K odebrání registrace vlastní domény, použijte [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) rutiny Powershellu a pak zadejte prázdný řetězec (`""`) pro `-CustomDomainName` hodnota argumentu.

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
* [Mapování vlastní domény do koncového bodu Azure Content Delivery Network (CDN)](../../cdn/cdn-map-content-to-custom-domain.md)
* [Přístup k objektům BLOB s použitím vlastních domén přes protokol HTTPS pomocí Azure CDN](storage-https-custom-domain-cdn.md)
* [Hostování statického webu ve službě Azure Blob storage (preview)](storage-blob-static-website.md)

---
title: Konfigurace vlastního názvu domény pro účet úložiště Azure | Dokumentace Microsoftu
description: Mapování vlastních kanonického názvu (CNAME) na koncový bod objektu Blob nebo web v účtu služby Azure Storage pomocí webu Azure portal.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/26/2018
ms.author: tamram
ms.component: blobs
ms.openlocfilehash: beacb39752b2af51aba4ddeb41c9bff68803df1b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46954768"
---
# <a name="configure-a-custom-domain-name-for-your-azure-storage-account"></a>Konfigurace vlastního názvu domény pro váš účet Azure Storage

Můžete nakonfigurovat vlastní doménu pro přístup k datům objektu blob v účtu úložiště Azure. Výchozí koncový bod pro úložiště objektů Blob je `<storage-account-name>.blob.core.windows.net`. Můžete také použít webový koncový bod generována jako součást [funkce statických webů (preview)](storage-blob-static-website.md). Pokud namapujete vlastní doménu a subdoménu jako **www.contoso.com** koncový bod objektu blob nebo web pro úložiště účtu, uživatelé můžou potom přístup k datům objektu blob v účtu úložiště pomocí této domény.

> [!IMPORTANT]
> Azure Storage zatím nativně nepodporuje protokol HTTPS s použitím vlastních domén. Aktuálně můžete [přístup k objektům BLOB s použitím vlastních domén přes protokol HTTPS pomocí Azure CDN](storage-https-custom-domain-cdn.md).
>

> [!NOTE]  
> Účty úložiště se aktuálně podporují jenom jeden vlastní název domény na jeden účet. To znamená, že vlastní název domény nelze mapovat na koncové body služeb web a objektů blob.

Následující tabulka ukazuje několik ukázky adresy URL pro data objektů blob v účtu úložiště s názvem **mystorageaccount**. Vlastní doména zaregistrovaná pro účet úložiště je **www.contoso.com**:

| Typ prostředku | Výchozí adresa URL | Adresa URL vlastní domény |
| --- | --- | --- | --- |
| Účet úložiště | http://mystorageaccount.blob.core.windows.net | http://www.contoso.com |
| Objekt blob |http://mystorageaccount.blob.core.windows.net/mycontainer/myblob | http://www.contoso.com/mycontainer/myblob |
| Kořenový kontejner | http://mystorageaccount.blob.core.windows.net/myblob nebo http://mystorageaccount.blob.core.windows.net/$root/myblob| http://www.contoso.com/myblob nebo http://www.contoso.com/$root/myblob |
| Web |  http://mystorageaccount. [zone].web.core.windows.net/$web/[indexdoc] nebo http://mystorageaccount. [ Zone].Web.Core.Windows.NET/[indexdoc] nebo http://mystorageaccount. [ Zone].Web.Core.Windows.NET/$Web nebo http://mystorageaccount. [ Zone].Web.Core.Windows.NET/ | http://www.contoso.com/$web nebo http://www.contoso.com/ nebo http://www.contoso.com/$web / [indexdoc] nebo http://www.contoso.com/[indexdoc] |

> [!NOTE]  
> Všechny příklady pro koncový bod služby Blob service níže platí také pro koncový bod webové služby.

## <a name="direct-vs-intermediary-domain-mapping"></a>Přímé porovnání zprostředkující domény mapování

Existují dva způsoby, jak vaši vlastní doménu. přejděte na koncový bod objektu blob pro účet úložiště: přímé CNAME mapování a použití *asverify* zprostředkující subdomény.

### <a name="direct-cname-mapping"></a>Přímé mapování CNAME

Metoda první a nejjednodušší, je vytvořit záznam kanonického názvu (CNAME), který mapuje vaši vlastní doménu a subdoménu přímo na koncový bod objektu blob. Záznam CNAME je funkce system (DNS) název domény, která mapuje zdrojovou doménu na cílovou doménu. V takovém případě je ve zdrojové doméně své vlastní domény a subdomény, například *www.contoso.com*. Cílová doména je vašeho koncového bodu služby objektů Blob, například *mystorageaccount.blob.core.windows.net*.

Přímé metody jsou obsaženy v [registrace vlastní domény](#register-a-custom-domain).

### <a name="intermediary-mapping-with-asverify"></a>Zprostředkující mapování *asverify*

Druhá metoda používá také záznamy CNAME, ale nejprve využívá speciální subdomény rozpoznávaných Azure, aby se zabránilo výpadkům: **asverify**.

Proces mapování vlastní domény do koncového bodu objektu blob může způsobit krátkému výpadku domény během registrace v [webu Azure portal](https://portal.azure.com). Pokud vaše vlastní doména se aktuálně podporují aplikace smlouvu o úrovni služeb (SLA), která nevyžaduje žádný výpadek, pak můžete použít Azure *asverify* subdoménu jako krok zprostředkující registrace. Tento zprostředkující krok zajistí, že uživatelé se můžou během mapování DNS přístup k vaší doméně.

Se věnuje metodě zprostředkující [zaregistrovat vlastní doménu pomocí *asverify* subdoménu](#register-a-custom-domain-using-the-asverify-subdomain).

## <a name="register-a-custom-domain"></a>Registrace vlastní domény
Tento postup použijte k registraci vaši vlastní doménu. Pokud máte obavu, informace o doméně stručně není k dispozici uživatelům nebo pokud vaší vlastní domény není aktuálně hostování aplikace. Azure DNS můžete použít ke konfiguraci vlastního názvu DNS pro úložiště objektů Blob v Azure. Další informace najdete v tématu popisujícím [použití Azure DNS k určení nastavení vlastní domény pro službu Azure](https://docs.microsoft.com/azure/dns/dns-custom-domain#blob-storage).

Pokud ve vaší vlastní doméně aktuálně podporuje aplikace, která nemůže mít žádné výpadky, postupujte podle pokynů uvedených v [zaregistrovat vlastní doménu pomocí *asverify* subdoménu](#register-a-custom-domain-using-the-asverify-subdomain).

Pokud chcete nakonfigurovat vlastní název domény, musíte vytvořit nový záznam CNAME v DNS. Určuje záznam CNAME, alias pro název domény. V takovém případě mapuje adresu vaši vlastní doménu na koncový bod úložiště objektů Blob v účtu úložiště.

Obvykle můžete spravovat nastavení DNS pro vaši doménu na webu vašeho registrátora domény. Každý Registrátor má podobné, ale mírně odlišné metodu pro určení záznam CNAME, ale koncept je stejný. Některé balíčky doména pro základní registrace nenabízejí konfigurace DNS, proto budete muset upgradovat registrační balíček vaší domény, než budete moct vytvořit záznam CNAME.

1. Přejděte do svého účtu úložiště v [webu Azure portal](https://portal.azure.com).
1. V části **služby BLOB SERVICE** v okně s nabídkou vyberte **vlastní domény** otevřít *vlastní domény* okno.
1. Přihlaste se k webu vašeho registrátora domény a přejděte na stránku pro správu DNS. Může to být v části, jako je **Název domény**, **DNS** nebo **Správa názvového serveru**.
1. Najděte část pro správu záznamů CNAME. Bude pravděpodobně nutné přejít na stránku rozšířeného nastavení a hledat slova **CNAME**, **Alias**, nebo **subdomény**.
1. Vytvoří nový záznam CNAME a zadejte subdoménu alias jako **www** nebo **fotografie**. Potom zadejte název hostitele, což je služba koncový bod služby Blob, ve formátu **mystorageaccount.blob.core.windows.net** (kde *mystorageaccount* je název vašeho účtu úložiště). Název hostitele pro použití se zobrazí u položky #1 *vlastní domény* okna portálu [webu Azure portal](https://portal.azure.com).
1. Do textového pole na *vlastní domény* okna portálu [webu Azure portal](https://portal.azure.com), zadejte název vlastní doménu včetně poddomény. Například, pokud je vaše doména **contoso.com** a je váš alias subdoménu **www**, zadejte **www.contoso.com**. Pokud je vaše subdoménu **fotografie**, zadejte **photos.contoso.com**. Se subdoménou *požadované*.
1. Vyberte **Uložit** na *vlastní domény* okno k registraci ve vaší vlastní doméně. Pokud registrace je úspěšný, zobrazí se oznámení na portálu účtu úložiště se úspěšně aktualizovala.

Jakmile nový záznam CNAME se rozšíří prostřednictvím DNS, vaši uživatelé můžete zobrazit data objektů blob s použitím vlastní domény, tak dlouho, dokud mají příslušná oprávnění.

## <a name="register-a-custom-domain-using-the-asverify-subdomain"></a>Registrace a s použitím vlastní domény *asverify* subdomény
Pomocí tohoto postupu můžete zaregistrovat vlastní domény Pokud vaší vlastní domény aktuálně podporuje aplikace se smlouvou SLA, která vyžaduje, aby existovat bez výpadků. Tím, že vytvoříte záznam CNAME, který se odkazuje z `asverify.<subdomain>.<customdomain>` k `asverify.<storageaccount>.blob.core.windows.net`, můžete předem zaregistrovat vaši doménu v Azure. Potom můžete vytvořit druhý záznam CNAME, který se odkazuje z `<subdomain>.<customdomain>` k `<storageaccount>.blob.core.windows.net`, v tomto okamžiku budete přesměrováni provozu do vaší vlastní domény na koncový bod služby blob.

**Asverify** subdoménu je speciální subdoména rozpoznána platformou Azure. Předponou v podobě `asverify` vlastní poddomény povolit Azure beze změny záznam DNS pro doménu rozpoznala vaši vlastní doménu. Při úpravě záznamu DNS pro doménu, se namapují na koncový bod služby blob došlo k výpadku.

1. Přejděte do svého účtu úložiště v [webu Azure portal](https://portal.azure.com).
1. V části **služby BLOB SERVICE** v okně s nabídkou vyberte **vlastní domény** otevřít *vlastní domény* okno.
1. Přihlaste se k webu vašeho poskytovatele DNS a přejděte na stránku pro správu DNS. Může to být v části, jako je **Název domény**, **DNS** nebo **Správa názvového serveru**.
1. Najděte část pro správu záznamů CNAME. Bude pravděpodobně nutné přejít na stránku rozšířeného nastavení a hledat slova **CNAME**, **Alias**, nebo **subdomény**.
1. Vytvoří nový záznam CNAME a zadejte subdoménu alias, který zahrnuje *asverify* subdomény. Například **asverify.www** nebo **asverify.photos**. Potom zadejte název hostitele, což je služba koncový bod služby Blob, ve formátu **asverify.mystorageaccount.blob.core.windows.net** (kde **mystorageaccount** je název vašeho účtu úložiště). Název hostitele pro použití se zobrazí v bodu #2 *vlastní domény* okna portálu [webu Azure portal](https://portal.azure.com).
1. Do textového pole na *vlastní domény* okna portálu [webu Azure portal](https://portal.azure.com), zadejte název vlastní doménu včetně poddomény. Nezahrnují *asverify*. Například, pokud je vaše doména **contoso.com** a je váš alias subdoménu **www**, zadejte **www.contoso.com**. Pokud je vaše subdoménu **fotografie**, zadejte **photos.contoso.com**. Vyžaduje se subdoménou.
1. Vyberte **použít nepřímé ověření CNAME** zaškrtávací políčko.
1. Vyberte **Uložit** na *vlastní domény* okno k registraci ve vaší vlastní doméně. Pokud registrace je úspěšný, zobrazí se oznámení na portálu s informacemi o tom, že váš účet úložiště se úspěšně aktualizovala. V tomto okamžiku se neověří vlastní doménu Azure, ale provoz do domény není dosud směrovány do vašeho účtu úložiště.
1. Zpět na web poskytovatele DNS a vytvořit další záznam CNAME, který mapuje vaše subdomény na koncový bod služby Blob service. Například zadejte subdoménu jako **www** nebo **fotografie** (bez *asverify*) a název hostitele jako **mystorageaccount.blob.core.windows.net**  (kde **mystorageaccount** je název vašeho účtu úložiště). S tímto krokem je dokončení registrace vlastní domény.
1. Nakonec můžete odstranit záznam CNAME, který jste vytvořili, který obsahuje **asverify** subdomény, protože bylo zapotřebí pouze jako zprostředkující kroky.

Jakmile nový záznam CNAME se rozšíří prostřednictvím DNS, vaši uživatelé můžete zobrazit data objektů blob s použitím vlastní domény, tak dlouho, dokud mají příslušná oprávnění.

## <a name="test-your-custom-domain"></a>Testování vlastní domény

Potvrďte, že vaše vlastní doména se mapuje skutečně na koncový bod služby Blob service, vytvoření objektu blob ve veřejném kontejneru v účtu úložiště. Potom ve webovém prohlížeči, použijte identifikátor URI v následujícím formátu a přístup k objektu blob:

`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Například můžete použít následující identifikátor URI pro přístup k webové formuláře v **myforms** kontejneru **photos.contoso.com** vlastní subdomény:

`http://photos.contoso.com/myforms/applicationform.htm`

## <a name="deregister-a-custom-domain"></a>Zrušení registrace vlastní domény

Zrušení registrace vlastní domény pro koncový bod služby Blob storage, použijte jednu z následujících postupů.

### <a name="azure-portal"></a>portál Azure

Na webu Azure Portal k odebrání nastavení vlastní domény, proveďte následující kroky:

1. Přejděte do svého účtu úložiště v [webu Azure portal](https://portal.azure.com).
1. V části **služby BLOB SERVICE** v okně s nabídkou vyberte **vlastní domény** otevřít *vlastní domény* okno.
1. Vymazání obsahu textového pole obsahující vlastní název domény.
1. Vyberte tlačítko **Uložit**.

Při vlastní domény byl úspěšně odebrán, zobrazí se oznámení na portálu s informacemi o tom, že váš účet úložiště se úspěšně aktualizovala.

### <a name="azure-cli"></a>Azure CLI

Použití [aktualizace účtu úložiště az](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_update) rozhraní příkazového řádku příkaz a zadejte prázdný řetězec (`""`) pro `--custom-domain` hodnota argumentu k odebrání registrace vlastní domény.

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

Použití [Set-AzureRmStorageAccount](/powershell/module/azurerm.storage/set-azurermstorageaccount) rutiny prostředí PowerShell a zadejte prázdný řetězec (`""`) pro `-CustomDomainName` hodnota argumentu k odebrání registrace vlastní domény.

* Formát příkazu:

  ```powershell
  Set-AzureRmStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Příklad příkazu:

  ```powershell
  Set-AzureRmStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```

## <a name="next-steps"></a>Další postup
* [Mapování vlastní domény do koncového bodu Azure Content Delivery Network (CDN)](../../cdn/cdn-map-content-to-custom-domain.md)
* [Přístup k objektům BLOB s použitím vlastních domén přes protokol HTTPS pomocí Azure CDN](storage-https-custom-domain-cdn.md)
* [Hostování statického webu ve službě Azure Blob Storage (Preview)](storage-blob-static-website.md)

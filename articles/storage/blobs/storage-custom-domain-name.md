---
title: Konfigurace vlastního názvu domény pro váš účet úložiště Azure | Microsoft Docs
description: Pomocí portálu Azure k mapování vlastní kanonický název (CNAME) na koncový bod objektu Blob nebo web v účtu Azure Storage.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 06/26/2018
ms.author: tamram
ms.openlocfilehash: 2f4267c25dfd31e6f1d5ae3a832be06b5ef6c828
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37017916"
---
# <a name="configure-a-custom-domain-name-for-your-azure-storage-account"></a>Konfigurace vlastního názvu domény pro váš účet úložiště Azure

Můžete nakonfigurovat vlastní doménu pro přístup k datům objektu blob v účtu úložiště Azure. Výchozí koncový bod pro úložiště objektů Blob je `<storage-account-name>.blob.core.windows.net`. Můžete také koncový bod webové jako součást [funkce statických webů (preview)](storage-blob-static-website.md). Pokud namapujete vlastních domén a subdomén jako **www.contoso.com** ke koncovému bodu objektů blob nebo webu pro úložiště účet, můžete uživatelům a přístup k datům objektu blob ve vašem účtu úložiště pomocí této domény.

> [!IMPORTANT]
> Úložiště Azure ještě nativně nepodporuje protokol HTTPS s vlastní domény. Můžete v danou chvíli [přístup k objektům BLOB pomocí vlastní domény přes HTTPS pomocí Azure CDN](storage-https-custom-domain-cdn.md).
>

> [!NOTE]  
> Účty úložiště v současné době podporují pouze jeden vlastní název domény pro účet. To znamená, že vlastní název domény nelze namapovat na koncové body služby webové a objektů blob.

Následující tabulka uvádí několik ukázkových adres URL pro data objektů blob v účtu úložiště s názvem **můj_účet_úložiště**. Vlastní doména zaregistrovaná pro účet úložiště je **www.contoso.com**:

| Typ prostředku | Výchozí adresa URL | Adresa URL vlastní domény |
| --- | --- | --- | --- |
| Účet úložiště | http://mystorageaccount.blob.core.windows.net | http://www.contoso.com |
| Objekt blob |http://mystorageaccount.blob.core.windows.net/mycontainer/myblob | http://www.contoso.com/mycontainer/myblob |
| Kořenový kontejner | http://mystorageaccount.blob.core.windows.net/myblob nebo http://mystorageaccount.blob.core.windows.net/$root/můj_objekt_blob| http://www.contoso.com/myblob nebo http://www.contoso.com/$root/můj_objekt_blob |
| Web |  http://mystorageaccount. [zone].web.core.windows.net/$web/[indexdoc] nebo http://mystorageaccount. [ Zone].Web.Core.Windows.NET/[indexdoc] nebo http://mystorageaccount. [ Zone].Web.Core.Windows.NET/$Web nebo http://mystorageaccount. [ Zone].Web.Core.Windows.NET/ | http://www.contoso.com/$web nebo http://www.contoso.com/ nebo http://www.contoso.com/$web / [indexdoc] nebo http://www.contoso.com/[indexdoc] |

> [!NOTE]  
> Všechny příklady pro koncový bod služby objektů Blob níže se rovněž vztahují na koncový bod webové služby.

## <a name="direct-vs-intermediary-domain-mapping"></a>Přímé oproti mapování zprostředkující domény

Existují dva způsoby, jak vaši vlastní doménu. přejděte na koncový bod objektu blob pro váš účet úložiště: přímé CNAME mapování a pomocí *asverify* zprostředkující subdomény.

### <a name="direct-cname-mapping"></a>Přímé mapování CNAME

Metoda první a nejjednodušší, je vytvořit záznam název v kanonickém tvaru (CNAME), který mapuje vaše vlastní domény a subdomény přímo na koncový bod objektů blob. Záznam CNAME je funkce system (DNS) název domény, která mapuje zdrojovou doménu do cílové domény. V takovém případě zdrojovou doménu je vlastní vlastní domény a subdomény, například *www.contoso.com*. Cílová doména je koncový bod služby objektů Blob, například *mystorageaccount.blob.core.windows.net*.

Přímá metoda je popsaná v [zaregistrovat vlastní doménu](#register-a-custom-domain).

### <a name="intermediary-mapping-with-asverify"></a>Zprostředkující mapování s *asverify*

Druhá metoda také používá záznamy CNAME, ale nejdřív využívá speciální subdoména rozpoznáno Azure výpadky: **asverify**.

Proces mapování vaši vlastní doménu na koncový bod objektu blob může způsobit na krátkou dobu výpadku pro doménu v registrace [portál Azure](https://portal.azure.com). Pokud vaše vlastní doména je aktuálně podporuje aplikace s dohodu o úrovni služeb (SLA) vyžadující nepřeruší, pak můžete použít Azure *asverify* subdomény jako zprostředkující registrace krok. Tento zprostředkující krok zajistí, že se uživatelé moct pro přístup k vaší doméně, zatímco probíhá mapování DNS.

Zprostředkující metoda je popsaná v [zaregistrovat vlastní domény pomocí *asverify* subdomény](#register-a-custom-domain-using-the-asverify-subdomain).

## <a name="register-a-custom-domain"></a>Zaregistrovat k vlastní doméně
Pomocí tohoto postupu registrace vaši vlastní doménu, pokud máte obavu, informace o doméně stručně není k dispozici pro vaše uživatele, nebo pokud vaše vlastní doména není aktuálně hostování aplikace. Azure DNS můžete nakonfigurovat vlastní název DNS vašeho úložiště objektů Blob v Azure. Další informace najdete v tématu [Azure DNS používá k poskytování nastavení vlastní domény pro službu Azure](https://docs.microsoft.com/azure/dns/dns-custom-domain#blob-storage).

Pokud vaše vlastní doména je aktuálně podporujete aplikaci, která nemůže mít žádné výpadky, postupujte podle pokynů uvedených v [zaregistrovat vlastní domény pomocí *asverify* subdomény](#register-a-custom-domain-using-the-asverify-subdomain).

Pokud chcete nakonfigurovat vlastní název domény, musíte vytvořit nový záznam CNAME ve službě DNS. Záznam CNAME Určuje alias pro název domény. V takovém případě mapuje adresu vaši vlastní doménu na koncový bod úložiště objektů Blob pro váš účet úložiště.

Obvykle můžete spravovat nastavení vaše doména DNS na webu registrátora domény. Každý Registrátor má podobné, ale poněkud liší metodu zadávání záznam CNAME, ale koncept je stejný. Některé balíčky registrace základní domény nenabízejí konfiguraci DNS, takže budete muset vaší domény registrační balíček s upgradem bylo možné vytvořit záznam CNAME.

1. Přejděte na svůj účet úložiště [portál Azure](https://portal.azure.com).
1. V části **služby objektů BLOB** v okně nabídky vyberte **vlastní domény** otevřete *vlastní domény* okno.
1. Přihlaste se k webu vašeho registrátora domény a přejděte na stránku pro správu DNS. Může to být v části, jako je **Název domény**, **DNS** nebo **Správa názvového serveru**.
1. Najděte část pro správu záznamů CNAME. Možná budete muset přejít na stránku Upřesnit nastavení a vyhledejte slova **CNAME**, **Alias**, nebo **subdomény**.
1. Vytvořit nový záznam CNAME a zadejte alias subdomény, jako **www** nebo **fotografie**. Potom zadejte název hostitele, který je váš koncový bod objektu Blob služby, ve formátu **mystorageaccount.blob.core.windows.net** (kde *můj_účet_úložiště* je název účtu úložiště). Název hostitele pro použití se zobrazí v položce #1 *vlastní domény* v okně [portál Azure](https://portal.azure.com).
1. Do textového pole na *vlastní domény* okno v [portál Azure](https://portal.azure.com), zadejte název vlastní domény, včetně subdoméně. Například, pokud vaše doména je **contoso.com** a je váš alias subdomény **www**, zadejte **www.contoso.com**. Pokud je vaše subdomény **fotografie**, zadejte **photos.contoso.com**. Je subdoménou *požadované*.
1. Vyberte **Uložit** na *vlastní domény* okno zaregistrovat vaši vlastní doménu. Pokud registrace úspěšná, zobrazí se portálu oznámení, že váš účet úložiště se úspěšně aktualizoval.

Jakmile vaše nový záznam CNAME se rozšíří přes DNS, vaši uživatelé můžete zobrazit data objektu blob pomocí vlastní domény, tak dlouho, dokud budou mít příslušná oprávnění.

## <a name="register-a-custom-domain-using-the-asverify-subdomain"></a>Zaregistrovat vlastní domény pomocí *asverify* subdomény
Pomocí tohoto postupu můžete zaregistrovat vaše vlastní domény Pokud vaše vlastní doména je aktuálně podporujete aplikace s SLA, která vyžaduje, aby se stát, že nedojde k žádnému výpadku. Vytvořit záznam CNAME, který odkazuje z `asverify.<subdomain>.<customdomain>` k `asverify.<storageaccount>.blob.core.windows.net`, můžete předem zaregistrovat doménu s Azure. Potom můžete vytvořit druhý CNAME, který odkazuje z `<subdomain>.<customdomain>` k `<storageaccount>.blob.core.windows.net`, na který bod přenosy na vlastní domény budete přesměrováni na koncový bod služby blob.

**Asverify** subdomény je speciální subdoména rozpoznáno Azure. Předponou `asverify` vlastní poddomény povolení Azure rozpoznat vaše vlastní doména beze změny záznam DNS pro doménu. Když upravíte záznam DNS pro doménu, budou mapována na koncový bod objektu blob bez výpadků.

1. Přejděte na svůj účet úložiště [portál Azure](https://portal.azure.com).
1. V části **služby objektů BLOB** v okně nabídky vyberte **vlastní domény** otevřete *vlastní domény* okno.
1. Přihlaste se k webu svého poskytovatele DNS a přejděte na stránku pro správu DNS. Může to být v části, jako je **Název domény**, **DNS** nebo **Správa názvového serveru**.
1. Najděte část pro správu záznamů CNAME. Možná budete muset přejít na stránku Upřesnit nastavení a vyhledejte slova **CNAME**, **Alias**, nebo **subdomény**.
1. Vytvořit nový záznam CNAME a zadejte alias subdomény, která zahrnuje *asverify* subdomény. Například **asverify.www** nebo **asverify.photos**. Potom zadejte název hostitele, který je váš koncový bod objektu Blob služby, ve formátu **asverify.mystorageaccount.blob.core.windows.net** (kde **můj_účet_úložiště** je název účtu úložiště). Název hostitele pro použití se zobrazí v položce #2 *vlastní domény* okno v [portál Azure](https://portal.azure.com).
1. Do textového pole na *vlastní domény* okno v [portál Azure](https://portal.azure.com), zadejte název vlastní domény, včetně subdoméně. Nezahrnovat *asverify*. Například, pokud vaše doména je **contoso.com** a je váš alias subdomény **www**, zadejte **www.contoso.com**. Pokud je vaše subdomény **fotografie**, zadejte **photos.contoso.com**. Subdoméně je povinný.
1. Vyberte **použít nepřímé ověření CNAME** zaškrtávací políčko.
1. Vyberte **Uložit** na *vlastní domény* okno zaregistrovat vaši vlastní doménu. Pokud registrace úspěšná, zobrazí se portálu oznámení s informacemi o tom, že váš účet úložiště se úspěšně aktualizoval. V tomto okamžiku se neověří vaše vlastní doména Azure, ale provoz k vaší doméně není ještě směrovány na váš účet úložiště.
1. Vraťte se na svého poskytovatele DNS web a vytvořte jiný záznam CNAME, který mapuje vaší subdomény vašeho koncového bodu služby objektů Blob. Můžete například zadat subdoméně jako **www** nebo **fotografie** (bez *asverify*) a název hostitele jako **mystorageaccount.blob.core.windows.net**  (kde **můj_účet_úložiště** je název účtu úložiště). Pro tento krok je dokončena registrace vaši vlastní doménu.
1. Nakonec můžete odstranit záznam CNAME, který jste vytvořili, obsahující **asverify** subdomény, protože byla potřeba pouze jako zprostředkující krok.

Jakmile vaše nový záznam CNAME se rozšíří přes DNS, vaši uživatelé můžete zobrazit data objektu blob pomocí vlastní domény, tak dlouho, dokud budou mít příslušná oprávnění.

## <a name="test-your-custom-domain"></a>Otestovat vaši vlastní doménu.

Pokud chcete potvrdit, že vaše vlastní doména je skutečně namapovaný na váš koncový bod služby objektů Blob, vytvořte objekt blob ve veřejném kontejneru v rámci účtu úložiště. Potom ve webovém prohlížeči, používání identifikátoru URI v následujícím formátu pro přístup k objektu blob:

`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Můžete například použít následující identifikátor URI pro přístup do webové formuláře **myforms** kontejneru **photos.contoso.com** vlastní subdomény:

`http://photos.contoso.com/myforms/applicationform.htm`

## <a name="deregister-a-custom-domain"></a>Zrušení registrace k vlastní doméně

Zrušit registraci vlastní doménu pro koncový bod služby Blob storage, použijte jednu z následujících postupů.

### <a name="azure-portal"></a>Azure Portal

Na portálu Azure k odebrání nastavení vlastní domény, proveďte následující kroky:

1. Přejděte na svůj účet úložiště [portál Azure](https://portal.azure.com).
1. V části **služby objektů BLOB** v okně nabídky vyberte **vlastní domény** otevřete *vlastní domény* okno.
1. Vymazání obsahu textového pole obsahující vlastní název domény.
1. Vyberte tlačítko **Uložit**.

Při vlastní doména byla úspěšně odebrána, zobrazí se portálu oznámení s informacemi o tom, že váš účet úložiště se úspěšně aktualizoval.

### <a name="azure-cli-20"></a>Azure CLI 2.0

Použití [aktualizace účtu úložiště az](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_update) rozhraní příkazového řádku příkaz a zadat prázdný řetězec (`""`) pro `--custom-domain` hodnota argumentu k odebrání registrace vlastní domény.

* Formát příkazu:

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* Příkaz příklad:

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```

### <a name="powershell"></a>PowerShell

Použití [Set AzureRmStorageAccount](/powershell/module/azurerm.storage/set-azurermstorageaccount) rutiny prostředí PowerShell a zadat prázdný řetězec (`""`) pro `-CustomDomainName` hodnota argumentu k odebrání registrace vlastní domény.

* Formát příkazu:

  ```powershell
  Set-AzureRmStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Příkaz příklad:

  ```powershell
  Set-AzureRmStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```

## <a name="next-steps"></a>Další postup
* [Namapovat vlastní doménu pro koncový bod Azure Content Delivery Network (CDN)](../../cdn/cdn-map-content-to-custom-domain.md)
* [Přístup k objektům BLOB pomocí vlastní domény přes HTTPS pomocí Azure CDN](storage-https-custom-domain-cdn.md)
* [Hostování statického webu ve službě Azure Blob Storage (Preview)](storage-blob-static-website.md)

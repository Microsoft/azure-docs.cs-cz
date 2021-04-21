---
title: Použití klíčů Customer-spravovaných k šifrování dat v mezipaměti HPC Azure
description: Použití Azure Key Vault s mezipamětí Azure HPC k řízení přístupu šifrovacího klíče místo používání výchozích šifrovacích klíčů spravovaných Microsoftem
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/20/2020
ms.author: v-erkel
ms.openlocfilehash: ae4c52ec1390166eccb0e73d6f81a8553c445b2e
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813285"
---
# <a name="use-customer-managed-encryption-keys-for-azure-hpc-cache"></a>Použití šifrovacích klíčů spravovaných zákazníkem pro mezipaměť HPC Azure

Azure Key Vault můžete použít k řízení vlastnictví klíčů používaných k šifrování vašich dat v mezipaměti HPC Azure. Tento článek vysvětluje, jak používat klíče spravované zákazníky pro šifrování dat v mezipaměti.

> [!NOTE]
> Všechna data uložená v Azure, včetně na discích mezipaměti, jsou v klidovém stavu zašifrovaná pomocí klíčů spravovaných Microsoftem. Pokud chcete spravovat klíče používané k šifrování vašich dat, stačí postupovat podle kroků v tomto článku.

Mezipaměť prostředí Azure HPC je taky chráněná [šifrováním hostitele virtuálních počítačů](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data) na spravovaných discích, které uchovávají data uložená v mezipaměti, a to i v případě, že pro disky mezipaměti přidáte klíč zákazníka. Přidáním klíče spravovaného zákazníkem pro dvojité šifrování získáte další úroveň zabezpečení pro zákazníky s vysokými nároky na zabezpečení. Podrobnosti najdete v tématu [šifrování služby Azure Disk Storage na straně serveru](../virtual-machines/disk-encryption.md) .

Existují tři kroky pro povolení šifrování klíče spravovaného zákazníkem pro mezipaměť prostředí Azure HPC:

1. Nastavte Azure Key Vault pro uložení klíčů.
1. Při vytváření mezipaměti prostředí Azure HPC zvolte šifrování klíče spravované zákazníkem a zadejte Trezor klíčů a klíč, který chcete použít.
1. Po vytvoření mezipaměti ji autorizujte pro přístup k trezoru klíčů.

Šifrování není zcela nastaveno, dokud jej nepovolíte z nově vytvořené mezipaměti (krok 3). Důvodem je to, že musíte předat identitě mezipaměti do trezoru klíčů, aby byl ověřený uživatel. Tento postup nelze provést před vytvořením mezipaměti, protože identita neexistuje, dokud nebude vytvořena mezipaměť.

Po vytvoření mezipaměti nemůžete měnit klíče spravované zákazníkem a klíče spravované Microsoftem. Pokud však vaše mezipaměť používá zákaznicky spravované klíče, můžete podle potřeby [změnit](#update-key-settings) šifrovací klíč, verzi klíče a trezor klíčů.

## <a name="understand-key-vault-and-key-requirements"></a>Principy trezoru klíčů a klíčových požadavků

Trezor klíčů a klíč musí splňovat tyto požadavky, aby fungovaly s mezipamětí Azure HPC cache.

Vlastnosti trezoru klíčů:

* **Předplatné** – použijte stejné předplatné, které se používá pro mezipaměť.
* **Oblast** – Trezor klíčů musí být ve stejné oblasti jako mezipaměť prostředí Azure HPC.
* **Cenová úroveň** – úroveň Standard je dostačující pro použití s mezipamětí Azure HPC.
* **Obnovitelné odstranění** – mezipaměť prostředí Azure HPC umožní obnovitelné odstranění, pokud už není v trezoru klíčů nakonfigurované.
* **Vyprázdnit ochranu** – vyprázdnit ochranu je nutné povolit.
* **Zásady přístupu** – výchozí nastavení jsou dostatečná.
* **Připojení k síti** – Azure HPC cache musí být schopný získat přístup k trezoru klíčů bez ohledu na nastavení koncového bodu, které zvolíte.

Klíčové vlastnosti:

* **Typ klíče** – RSA
* **Velikost klíče RSA** – 2048
* **Povoleno** – Ano

Přístupová oprávnění trezoru klíčů:

* Uživatel, který vytváří mezipaměť prostředí Azure HPC, musí mít oprávnění ekvivalentní [roli přispěvatele Key Vault](../role-based-access-control/built-in-roles.md#key-vault-contributor). K nastavení a správě Azure Key Vault je potřeba mít stejná oprávnění.

  Pro další informace si přečtěte [zabezpečený přístup k trezoru klíčů](../key-vault/general/security-features.md) .

## <a name="1-set-up-azure-key-vault"></a>1. nastavení Azure Key Vault

Trezor klíčů a klíč můžete nastavit před vytvořením mezipaměti nebo v rámci vytváření mezipaměti. Zajistěte, aby tyto prostředky splňovaly požadavky [uvedené výše](#understand-key-vault-and-key-requirements).

V okamžiku vytvoření mezipaměti musíte zadat trezor, klíč a verzi klíče, které se mají použít pro šifrování mezipaměti.

Podrobnosti najdete v [dokumentaci k Azure Key Vault](../key-vault/general/overview.md) .

> [!NOTE]
> Azure Key Vault musí používat stejné předplatné a musí být ve stejné oblasti jako mezipaměť prostředí Azure HPC. Ujistěte se, že oblast, kterou zvolíte, [podporuje oba produkty](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=hpc-cache,key-vault).

## <a name="2-create-the-cache-with-customer-managed-keys-enabled"></a>2. vytvoření mezipaměti s povolenými klíči spravovanými zákazníky

Když vytváříte mezipaměť prostředí Azure HPC, musíte zadat zdroj šifrovacího klíče. Postupujte podle pokynů v tématu [vytvoření mezipaměti Azure HPC](hpc-cache-create.md)a zadáním klíče trezoru klíčů a klíče na stránce **klíče pro šifrování disku** . Během vytváření mezipaměti můžete vytvořit nový trezor klíčů a klíč.

> [!TIP]
> Pokud se nezobrazí stránka **šifrovací klíče disku** , ujistěte se, že je vaše mezipaměť v některé z [podporovaných oblastí](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=hpc-cache,key-vault).

Uživatel, který vytváří mezipaměť, musí mít oprávnění rovna [roli přispěvatele Key Vault](../role-based-access-control/built-in-roles.md#key-vault-contributor) nebo vyšší.

1. Kliknutím na toto tlačítko povolíte soukromě spravované klíče. Po změně tohoto nastavení se zobrazí nastavení trezoru klíčů.

1. Kliknutím na **Vybrat Trezor klíčů** otevřete stránku výběr klíče. Vyberte nebo vytvořte Trezor klíčů a klíč pro šifrování dat na discích této mezipaměti.

   Pokud se Azure Key Vault v seznamu nezobrazí, ověřte tyto požadavky:

   * Je mezipaměť ve stejném předplatném jako Trezor klíčů?
   * Je mezipaměť ve stejné oblasti jako Trezor klíčů?
   * Existuje síťové připojení mezi Azure Portal a trezorem klíčů?

1. Po výběru trezoru Vyberte jednotlivý klíč z dostupných možností nebo vytvořte nový klíč. Klíč musí být 2048 klíč RSA.

1. Zadejte verzi vybraného klíče. Přečtěte si další informace o tom, jak se správou verzí v [dokumentaci Azure Key Vault](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning).

Pokračujte ve zbývajících specifikacích a vytvořte mezipaměť, jak je popsáno v tématu [vytvoření mezipaměti prostředí Azure HPC](hpc-cache-create.md).

## <a name="3-authorize-azure-key-vault-encryption-from-the-cache"></a>3. autorizace Azure Key Vault šifrování z mezipaměti
<!-- header is linked from create article, update if changed -->

Po několika minutách se nová mezipaměť prostředí Azure HPC objeví ve vašem Azure Portal. Přejděte na stránku **Přehled** a udělte jí přístup k vašemu Azure Key Vault a povolíte šifrování klíče spravovaného zákazníkem.

> [!TIP]
> Mezipaměť se může zobrazit v seznamu prostředků předtím, než se vymaže zprávy "probíhá nasazení". Po uplynutí minuty nebo dvou informací se seznam prostředků vraťte na oznámení o úspěšnosti.

Tento proces se dvěma kroky je nezbytný, protože instance mezipaměti prostředí Azure HPC potřebuje k předání Azure Key Vault pro autorizaci identitu. Identita mezipaměti neexistuje až po dokončení počátečních kroků vytváření.

> [!NOTE]
> Po vytvoření mezipaměti musíte ověřit šifrování během 90 minut. Pokud tento krok nedokončíte, dojde k vypršení časového limitu mezipaměti a selhání. Neúspěšná mezipaměť musí být vytvořena znovu, nelze ji opravit.

Mezipaměť zobrazuje stav čekání na **klíč**. Kliknutím na tlačítko **Povolit šifrování** v horní části stránky autorizujete mezipaměť pro přístup k zadanému trezoru klíčů.

![snímek stránky s přehledem mezipaměti na portálu s zvýrazněním tlačítka povolit šifrování (horní řádek) a stav: čekání na klíč](media/waiting-for-key.png)

Klikněte na **Povolit šifrování** a potom kliknutím na tlačítko **Ano** autorizujte mezipaměť pro použití šifrovacího klíče. Tato akce také umožňuje ochranu před vymazáním a vyprázdněním (Pokud ještě není povolená) v trezoru klíčů.

![snímek stránky s přehledem mezipaměti na portálu s hlavičkou v horní části, která žádá uživatele o povolení šifrování kliknutím na Ano](media/enable-keyvault.png)

Poté, co mezipaměť požaduje přístup k trezoru klíčů, může vytvořit a zašifrovat disky, které ukládají data v mezipaměti.

Po autorizaci šifrování provede mezipaměť prostředí Azure HPC několik dalších minut instalačního programu, aby se vytvořily šifrované disky a související infrastruktura.

## <a name="update-key-settings"></a>Aktualizovat nastavení klíče

Můžete změnit Trezor klíčů, klíč nebo verzi klíče pro mezipaměť z Azure Portal. Kliknutím na odkaz nastavení **šifrování** v mezipaměti otevřete stránku **nastavení klíče zákazníka** .

Nemůžete změnit mezipaměť mezi uživatelsky spravovanými klíči a klíči spravovanými systémem.

![snímek obrazovky nastavení klíčů zákazníka získáte tak, že kliknete na nastavení > šifrování ze stránky mezipaměti v Azure Portal](media/change-key-click.png)

Klikněte na odkaz **změnit klíč** a potom kliknutím na **změnit Trezor klíčů, klíč nebo verzi** Otevřete selektor klíčů.

![snímek obrazovky "vybrat klíč z Azure Key Vault" se třemi selektory rozevíracího seznamu pro výběr trezoru klíčů, klíče a verze](media/select-new-key.png)

V seznamu se zobrazují trezory klíčů ve stejném předplatném a stejné oblasti jako Tato mezipaměť.

Po výběru nových hodnot šifrovacího klíče klikněte na **Vybrat**. Zobrazí se stránka s potvrzením, která má nové hodnoty. Kliknutím na **Uložit** dokončete výběr.

![snímek obrazovky s potvrzením na stránce s tlačítkem Uložit vlevo nahoře](media/save-key-settings.png)

## <a name="read-more-about-customer-managed-keys-in-azure"></a>Další informace o klíčích spravovaných zákazníkem v Azure

Tyto články vysvětlují Další informace o používání Azure Key Vault a klíčů spravovaných zákazníkem k šifrování dat v Azure:

* [Přehled šifrování Azure Storage](../storage/common/storage-service-encryption.md)
* [Šifrování disku pomocí klíčů spravovaných zákazníkem](../virtual-machines/disk-encryption.md#customer-managed-keys) – dokumentace k používání Azure Key Vault se spravovanými disky, což je podobný scénář mezipaměti HPC Azure

## <a name="next-steps"></a>Další kroky

Po vytvoření mezipaměti HPC Azure a ověření šifrování založeného na Key Vault pokračujte v nastavení mezipaměti tím, že udělíte přístup k vašim zdrojům dat.

* [Přidání cílů úložiště](hpc-cache-add-storage.md)

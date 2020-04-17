---
title: Použití klíčů vytvořených zákazníky k šifrování dat v azure hpc cache
description: Jak používat Azure Key Vault s Azure HPC Cache k řízení přístupu k šifrovacímu klíči namísto použití výchozích šifrovacích klíčů spravovaných Microsoftem
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: a31979763dd1ab5d8f289deef0e30cce27bb0df4
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538866"
---
# <a name="use-customer-managed-encryption-keys-for-azure-hpc-cache"></a>Použití šifrovacích klíčů spravovaných zákazníkem pro Azure HPC Cache

Azure Key Vault můžete použít k řízení vlastnictví klíčů používaných k šifrování dat v Azure HPC Cache. Tento článek vysvětluje, jak používat klíče spravované zákazníkem pro šifrování dat mezipaměti.

> [!NOTE]
> Všechna data uložená v Azure, včetně na disky mezipaměti, se ve výchozím nastavení šifrují pomocí klíčů spravovaných microsoftem. Pokud chcete spravovat klíče používané k šifrování dat, postupujte podle kroků uvedených v tomto článku.

Tato funkce je dostupná jenom v těchto oblastech Azure:

* USA – východ
* USA – středojih
* USA – západ 2

Existují tři kroky, jak povolit šifrování klíčů spravovaných zákazníkem pro Azure HPC Cache:

1. Nastavte trezor klíčů Azure pro uložení klíčů.
1. Při vytváření mezipaměti Azure HPC zvolte šifrování klíčů spravované zákazníkem a určete trezor klíčů a klíč, který chcete použít.
1. Po vytvoření mezipaměti ji autorizujte pro přístup k trezoru klíčů.

Šifrování není zcela nastaveno, dokud jej neautorizujete z nově vytvořené mezipaměti (krok 3). Je to proto, že je nutné předat identitu mezipaměti do trezoru klíčů, aby byl autorizovaným uživatelem. Před vytvořením mezipaměti to nelze provést, protože identita neexistuje, dokud není mezipaměť vytvořena.

Po vytvoření mezipaměti nelze měnit mezi klíči spravovanými zákazníky a klíči spravovanými společností Microsoft. Pokud však vaše mezipaměť používá klíče spravované zákazníkem, můžete podle potřeby [změnit](#update-key-settings) šifrovací klíč, verzi klíče a trezor klíčů.

## <a name="understand-key-vault-and-key-requirements"></a>Principy trezoru klíčů a požadavků na klíče

Trezor klíčů a klíč musí splňovat tyto požadavky pro práci s Azure HPC Cache.

Vlastnosti trezoru klíčů:

* **Předplatné** – použijte stejné předplatné, které se používá pro mezipaměť.
* **Oblast** – trezor klíčů musí být ve stejné oblasti jako mezipaměť HPC Azure.
* **Cenová úroveň** – úroveň Standard je dostatečná pro použití s mezipamětí Azure HPC.
* **Obnovitelné odstranění** – Azure HPC Cache povolí obnovitelné odstranění, pokud ještě není nakonfigurované v trezoru klíčů.
* **Ochrana proti vymazání** – ochrana proti vymazání musí být povolena.
* **Zásady přístupu** – výchozí nastavení jsou dostatečná.
* **Připojení k síti** – Azure HPC Cache musí mít přístup k trezoru klíčů bez ohledu na nastavení koncového bodu, které zvolíte.

Klíčové vlastnosti:

* **Typ klíče** - RSA
* **Velikost klíče RSA** - 2048
* **Povoleno** – ano

Přístupová oprávnění trezoru klíčů:

* Uživatel, který vytvoří mezipaměť Azure HPC, musí mít oprávnění ekvivalentní [roli přispěvatele trezoru klíčů](../role-based-access-control/built-in-roles.md#key-vault-contributor). Stejná oprávnění jsou potřeba k nastavení a správě Azure Key Vault.

  Další informace načtete [na článek Zabezpečený přístup k trezoru klíčů.](../key-vault/key-vault-secure-your-key-vault.md)

## <a name="1-set-up-azure-key-vault"></a>1. Nastavení trezoru klíčů Azure

Před vytvořením mezipaměti můžete nastavit trezor klíčů a klíč nebo to provést jako součást vytváření mezipaměti. Ujistěte se, že tyto zdroje splňují [výše](#understand-key-vault-and-key-requirements)uvedené požadavky .

V době vytváření mezipaměti je nutné zadat trezor, klíč a verzi klíče, která bude pro šifrování mezipaměti použít.

Podrobnosti načtete v [dokumentaci k úložišti klíčů Azure.](../key-vault/key-vault-overview.md)

> [!NOTE]
> Azure Key Vault musí používat stejné předplatné a být ve stejné oblasti jako azure hpc cache. Použijte jednu z podporovaných oblastí uvedených na začátku tohoto článku.

## <a name="2-create-the-cache-with-customer-managed-keys-enabled"></a>2. Vytvoření mezipaměti s povolenými klíči spravovanými zákazníky

Při vytváření mezipaměti Azure HPC je nutné zadat zdroj šifrovacího klíče. Postupujte podle pokynů v [tématu Vytvoření mezipaměti Azure HPC](hpc-cache-create.md)a zadejte trezor klíčů a klíč na stránce **klíče šifrování disku.** Během vytváření mezipaměti můžete vytvořit nový trezor klíčů a klíč.

> [!TIP]
> Pokud se stránka **klíče šifrování disku** nezobrazí, zkontrolujte, zda je mezipaměť v jedné z podporovaných oblastí.

Uživatel, který vytvoří mezipaměť, musí mít oprávnění rovnající se [roli přispěvatele trezoru klíčů](../role-based-access-control/built-in-roles.md#key-vault-contributor) nebo vyšší.

1. Kliknutím na tlačítko povolíte soukromě spravované klíče. Po změně tohoto nastavení se zobrazí nastavení trezoru klíčů.

1. Kliknutím na **Vybrat trezor klíčů** otevřete stránku výběru klíčů. Zvolte nebo vytvořte trezor klíčů a klíč pro šifrování dat na discích této mezipaměti.

   Pokud se váš trezor klíčů Azure v seznamu nezobrazuje, zkontrolujte tyto požadavky:

   * Je mezipaměť ve stejném předplatném jako trezor klíčů?
   * Je mezipaměť ve stejné oblasti jako trezor klíčů?
   * Je síťové připojení mezi portálem Azure a trezoru klíčů?

1. Po výběru trezoru vyberte jednotlivé klávesy z dostupných možností nebo vytvořte nový klíč. Klíč musí být 2048bitový klíč RSA.

1. Zadejte verzi pro vybraný klíč. Další informace o správu verzí v [dokumentaci k úložišti klíčů Azure](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning).

Pokračujte se zbývajícími specifikacemi a vytvořte mezipaměť, jak je popsáno v [části Vytvoření mezipaměti Azure HPC](hpc-cache-create.md).

## <a name="3-authorize-azure-key-vault-encryption-from-the-cache"></a>3. Autorizace šifrování Azure Key Vault z mezipaměti
<!-- header is linked from create article, update if changed -->

Po několika minutách se na vašem webu Azure Portal zobrazí nová mezipaměť HPC Azure. Přejděte na stránku **Přehled** a autorizujte ji pro přístup k úložišti klíčů Azure a povolte šifrování klíčů spravované zákazníkem. (Mezipaměť se může zobrazit v seznamu prostředků před vymazáním zpráv "probíhá nasazení".)

Tento dvoustupňový proces je nezbytný, protože instance Azure HPC Cache potřebuje identitu, aby se předala trezoru klíčů Azure pro autorizaci. Identita mezipaměti neexistuje, dokud nebudou dokončeny počáteční kroky vytvoření.

> [!NOTE]
> Šifrování je nutné autorizovat do 90 minut po vytvoření mezipaměti. Pokud tento krok nedokončíte, dojde k časovému uznamu mezipaměti a nezdaří se. Neúspěšná mezipaměť musí být znovu vytvořena, nelze ji opravit.

Mezipaměť zobrazuje stav **Čekání na klíč**. Kliknutím na tlačítko **Povolit šifrování** v horní části stránky autorizujete, aby mezipaměť přistupovala k zadanému trezoru klíčů.

![snímek obrazovky stránky přehledu mezipaměti na portálu se zvýrazněním na tlačítku Povolit šifrování (horní řádek) a Stav: Čekání na klíč](media/waiting-for-key.png)

Klikněte na **Povolit šifrování** a potom kliknutím na tlačítko **Ano** autorizujte mezipaměť k použití šifrovacího klíče. Tato akce také umožňuje odstranění a vymazání ochrany pomocí měkkého odstranění (pokud již není povoleno) v trezoru klíčů.

![snímek obrazovky stránky přehledu mezipaměti na portálu s bannerovou zprávou v horní části, která žádá uživatele o povolení šifrování kliknutím na tlačítko ano](media/enable-keyvault.png)

Poté, co mezipaměť požádá o přístup k trezoru klíčů, může vytvořit a zašifrovat disky, které ukládají data uložená v mezipaměti.

Po autorizaci šifrování prochází Azure HPC Cache několika dalšími minutami instalace a vytvoří šifrované disky a související infrastrukturu.

## <a name="update-key-settings"></a>Aktualizovat nastavení klíče

Můžete změnit trezor klíčů, klíč nebo verzi klíče pro vaši mezipaměť z webu Azure Portal. Kliknutím na odkaz **Nastavení šifrování** mezipaměti otevřete stránku Nastavení klíče **zákazníka.** (Mezi klíči spravovanými zákazníky a klíči spravovanými systémem nelze změnit mezi mezipamětí.)

![snímek obrazovky stránky Nastavení zákaznických klíčů, které bylo dosaženo kliknutím na Nastavení > šifrování na stránce mezipaměti na webu Azure Portal](media/change-key-click.png)

Klikněte na odkaz **Změnit klíč** a potom kliknutím na Změnit **trezor klíčů, klíč nebo verzi** otevřete volič klíčů.

![Snímek obrazovky stránky "Vybrat klíč z úložiště klíčů Azure" se třemi rozevíracími voliči pro výběr trezoru klíčů, klíče a verze](media/select-new-key.png)

Trezory klíčů ve stejném předplatném a ve stejné oblasti jako tato mezipaměť jsou zobrazeny v seznamu.

Po výběru nových hodnot šifrovacího klíče klepněte na **tlačítko Vybrat**. Zobrazí se stránka s potvrzením s novými hodnotami. Kliknutím na **Uložit** výběr dokončíte.

![snímek obrazovky s potvrzovací stránkou s tlačítkem Uložit vlevo nahoře](media/save-key-settings.png)

## <a name="read-more-about-customer-managed-keys-in-azure"></a>Další informace o klíčích spravovaných zákazníky v Azure

Tyto články vysvětlují další informace o používání Azure Key Vault a klíčů spravovaných zákazníky k šifrování dat v Azure:

* [Přehled šifrování úložiště Azure](../storage/common/storage-service-encryption.md)
* [Šifrování disku pomocí klíčů spravovaných zákazníkem](../virtual-machines/linux/disk-encryption.md#customer-managed-keys) – dokumentace pro použití služby Azure Key Vault a spravovaných disků, což je podobné procesu používanému v azure hpc cache

## <a name="next-steps"></a>Další kroky

Po vytvoření mezipaměti Azure HPC a autorizovaného šifrování založeného na úložišti klíčů pokračujte v nastavování mezipaměti tím, že jí uděláte přístup ke zdrojům dat.

* [Přidání cílů úložiště](hpc-cache-add-storage.md)

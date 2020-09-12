---
title: Běžné dotazy týkající se služby Azure Resource stěhovací?
description: Získejte odpovědi na běžné otázky týkající se Azure Resource stěhovacího.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 09/07/2020
ms.author: raynew
ms.openlocfilehash: 520c2d4fd258bfab5a5a1e0abf890d58bb98fbdc
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89652974"
---
# <a name="common-questions"></a>Časté dotazy

Tento článek obsahuje odpovědi na běžné otázky týkající se služby [Azure Resource stěhovací](overview.md).

## <a name="general"></a>Obecné

### <a name="is-resource-mover-generally-available"></a>Je stěhovací společnosti všeobecně dostupná?

Resource stěhovací je momentálně ve verzi Public Preview. Produkční úlohy se podporují.

## <a name="region-move"></a>Přesunutí oblasti

### <a name="what-resources-can-i-move-across-regions-using-resource-mover"></a>Jaké prostředky je možné přesouvat mezi oblastmi pomocí Resource Centre?

Pomocí nástroje Resource Centre můžete aktuálně přesouvat následující prostředky v různých oblastech:

- Virtuální počítače Azure a přidružené disky
- Síťové karty
- Skupiny dostupnosti 
- Virtuální sítě Azure 
- Veřejné IP adresy skupiny zabezpečení sítě (skupin zabezpečení sítě)
- Interní a veřejné nástroje pro vyrovnávání zatížení 
- Databáze SQL Azure a elastické fondy

### <a name="where-is-the-metadata-about-a-region-move-stored"></a>Kde se ukládají metadata týkající se přesunu oblasti?

Je uložený v databázi [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) a ve [službě Azure Blob Storage](../storage/common/storage-service-encryption.md)v předplatném Microsoftu.

### <a name="is-the-collected-metadata-encrypted"></a>Jsou shromážděná metadata zašifrovaná?

Ano, jak při přenosu, tak i v klidovém režimu.
- Během přenosu se metadata bezpečně odesílají do služby Resource stěhovací přes Internet pomocí protokolu HTTPS.
- V úložišti se metadata šifrují.

### <a name="how-is-managed-identity-used-in-resource-mover"></a>Jak se používá spravovaná identita v nástroji Resource stěhovací?

[Spravovaná identita](../active-directory/managed-identities-azure-resources/overview.md) (dřív označovaná jako identita spravované služby (MIS)) poskytuje služby Azure s automaticky spravovanou identitou ve službě Azure AD.
- Resource stěhovací používá spravovanou identitu, aby mohl přistupovat k předplatným Azure, aby bylo možné přesouvat prostředky do různých oblastí.
- Kolekce přesunů potřebuje identitu přiřazenou systémem a přístup k předplatnému, které obsahuje prostředky, které přesouváte.

- Pokud přesouváte prostředky do různých oblastí portálu, k tomuto procesu dochází automaticky.
- Pokud přesouváte prostředky pomocí prostředí PowerShell, spustíte rutiny k přiřazení identity přiřazené systému do kolekce a potom přiřadíte roli se správnými oprávněními k předplatnému objektu zabezpečení identity. 

### <a name="what-managed-identity-permissions-does-resource-mover-need"></a>Jaká oprávnění spravovaná identita potřebuje dopravení prostředků?

Spravovaná identita Azure Resource stěhovací potřebuje aspoň tato oprávnění: 

- Oprávnění k zápisu a vytváření prostředků v předplatném uživatele, která jsou k dispozici v roli *Přispěvatel* . 
- Oprávnění k vytváření přiřazení rolí. Obvykle k dispozici s rolemi *vlastník* nebo *Správce přístupu uživatelů* nebo s vlastní rolí, která má přiřazené *přiřazení Microsoft. Authorization/role/oprávnění k zápisu* . Toto oprávnění není nutné, pokud je spravované identitě prostředku sdílení dat již udělen přístup k úložišti dat Azure. 
 
Když na portálu přidáte prostředky do centra pro řízení zdrojů, budou se oprávnění zpracovávat automaticky, pokud má uživatel oprávnění popsaná výše. Pokud přidáte prostředky pomocí PowerShellu, přiřadíte oprávnění ručně.

> [!IMPORTANT]
> Důrazně doporučujeme, abyste nezměnili ani neodebrali přiřazení rolí identity. 

### <a name="what-should-i-do-if-i-dont-have-permissions-to-assign-role-identity"></a>Co mám dělat, když nemám oprávnění k přiřazení identity role?

**Možná příčina** | **Doporučení**
--- | ---
Nejste *přispěvatelem* a *správcem přístupu uživatele* (nebo *vlastníkem*), když přidáte prostředek poprvé. | Použijte účet s oprávněním *Přispěvatel* a *Správce přístupu uživatele* (nebo *vlastníka*) pro předplatné.
Spravovaná identita pro prostředek stěhovací společnosti nemá požadovanou roli. | Přidejte role přispěvatel a správce přístupu uživatele.
Spravovaná identita zdrojového stěhovacího prostředku se obnovila na *hodnotu None*. | Znovu povolte identitu přiřazenou systémem v kolekci Move > **identity**. Případně Přidejte prostředek znovu do části **Přidat prostředky**, která má stejnou věc.  
Předplatné se přesunulo do jiného tenanta. | Zakažte a pak povolte spravovanou identitu pro kolekci Move.


## <a name="next-steps"></a>Další kroky

[Přečtěte si další informace](about-move-process.md) o komponentách Resource stěhovací a procesu přesunutí.

---
title: Běžné dotazy týkající se služby Azure Resource stěhovací?
description: Získejte odpovědi na běžné otázky týkající se Azure Resource stěhovacího.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: raynew
ms.openlocfilehash: 68e5f937b8ad8367abf488598bda311a39d462c6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90600649"
---
# <a name="common-questions"></a>Časté dotazy

Tento článek obsahuje odpovědi na běžné otázky týkající se služby [Azure Resource stěhovací](overview.md).

## <a name="general"></a>Obecné

### <a name="is-resource-mover-generally-available"></a>Je stěhovací společnosti všeobecně dostupná?

Resource stěhovací je momentálně ve verzi Public Preview. Produkční úlohy se podporují.



## <a name="moving-across-regions"></a>Přesun mezi oblastmi

### <a name="can-i-move-resources-across-any-regions"></a>Můžu přesouvat prostředky napříč různými oblastmi?

V současné době můžete přesunout prostředky z libovolné zdrojové veřejné oblasti do libovolné cílové veřejné oblasti v závislosti na [typech prostředků dostupných v této oblasti](https://azure.microsoft.com/global-infrastructure/services/). Přesunutí prostředků v Azure Government oblastech není v současné době podporováno.

### <a name="what-resources-can-i-move-across-regions-using-resource-mover"></a>Jaké prostředky je možné přesouvat mezi oblastmi pomocí Resource Centre?

Pomocí nástroje Resource Centre můžete aktuálně přesouvat následující prostředky v různých oblastech:

- Virtuální počítače Azure a přidružené disky
- Síťové karty
- Skupiny dostupnosti 
- Virtuální sítě Azure 
- Veřejné IP adresy
- Skupiny zabezpečení sítě (NSG)
- Interní a veřejné nástroje pro vyrovnávání zatížení 
- Databáze SQL Azure a elastické fondy


### <a name="can-i-move-resources-across-subscriptions-when-i-move-them-across-regions"></a>Můžu po přesunu mezi oblastmi přesunout prostředky mezi předplatnými?

Po přesunutí prostředků do cílové oblasti můžete toto předplatné změnit. [Přečtěte si další informace](../azure-resource-manager/management/move-resource-group-and-subscription.md) o přesouvání prostředků do jiného předplatného. 

### <a name="where-is-the-metadata-for-moving-across-regions-stored"></a>Kde se ukládají metadata pro přesun mezi oblastmi, které jsou uložené?

Je uložený v databázi [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) a ve [službě Azure Blob Storage](../storage/common/storage-service-encryption.md)v předplatném Microsoftu. V současné době se metadata ukládají do Východní USA 2 a Severní Evropa. Rozšíříme Toto pokrytí do dalších oblastí. Neomezuje se tím přesunutí prostředků mezi žádné veřejné oblasti.

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

Spravovaná identita služby Azure Resource Mover musí mít alespoň tato oprávnění: 

- Oprávnění k zápisu a vytváření prostředků v předplatném uživatele, která jsou k dispozici v roli *Přispěvatel* . 
- Oprávnění k vytváření přiřazení rolí. Obvykle k dispozici s rolemi *vlastník* nebo *Správce přístupu uživatelů* nebo s vlastní rolí, která má přiřazené *přiřazení Microsoft. Authorization/role/oprávnění k zápisu* . Toto oprávnění není nutné, pokud je spravované identitě prostředku sdílení dat již udělen přístup k úložišti dat Azure. 
 
Pokud přidáte prostředky v centru Resource Mover na portálu, oprávnění se zajistí automaticky za předpokladu, že uživatel má výše popsaná oprávnění. Pokud přidáte prostředky pomocí PowerShellu, přiřadíte oprávnění ručně.

> [!IMPORTANT]
> Důrazně doporučujeme, abyste nezměnili ani neodebrali přiřazení rolí identity. 

### <a name="what-should-i-do-if-i-dont-have-permissions-to-assign-role-identity"></a>Co mám dělat, když nemám oprávnění k přiřazení identity role?

**Možná příčina** | **Doporučení**
--- | ---
Nejste *přispěvatelem* a *správcem přístupu uživatele* (nebo *vlastníkem*), když přidáte prostředek poprvé. | Použijte účet s oprávněním *Přispěvatel* a *Správce přístupu uživatele* (nebo *vlastníka*) pro předplatné.
Spravovaná identita pro prostředek stěhovací společnosti nemá požadovanou roli. | Přidejte role přispěvatel a správce přístupu uživatele.
Spravovaná identita zdrojového stěhovacího prostředku se obnovila na *hodnotu None*. | Znovu povolte identitu přiřazenou systémem v kolekci Move > **identity**. Případně Přidejte prostředek znovu do části **Přidat prostředky**, která má stejnou věc.  
Předplatné se přesunulo do jiného tenanta. | Zakažte a pak povolte spravovanou identitu pro kolekci Move.

### <a name="how-can-i-do-multiple-moves-together"></a>Jak můžu provést více přesunů najednou?

Podle potřeby změňte kombinace zdrojů a cílů pomocí možnosti změnit na portálu.

## <a name="next-steps"></a>Další kroky

[Přečtěte si další informace](about-move-process.md) o komponentách Resource stěhovací a procesu přesunutí.

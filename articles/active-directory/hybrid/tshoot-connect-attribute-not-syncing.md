---
title: Řešení potíží s atributem, který se nesynchronizuje v Azure AD Connect | Microsoft Docs
description: V tomto tématu najdete postup řešení potíží se synchronizací atributů pomocí úlohy řešení potíží.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6df1347eab57a6971fe2e39c0a55869c8f23939
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91317483"
---
# <a name="troubleshoot-an-attribute-not-synchronizing-in-azure-ad-connect"></a>Řešení potíží s atributem, který se nesynchronizuje v Azure AD Connect

## <a name="recommended-steps"></a>**Doporučené kroky**

Než prozkoumáte problémy se synchronizací atributů, Podívejme se na **Azure AD Connect** proces synchronizace:

  ![Proces synchronizace Azure AD Connect](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologie**

* **Cs:** Místo konektoru, tabulka v databázi.
* **MV:** Metaverse, tabulka v databázi.
* **Služba AD:** Služba Active Directory
* **AAD:** Azure Active Directory

### <a name="synchronization-steps"></a>**Kroky synchronizace**

* Import ze služby AD: objekty služby Active Directory se přenesou do služby AD CS.

* Import z AAD: objekty Azure Active Directory se přenesou do služby AAD CS.

* Synchronizace: **pravidla pro příchozí synchronizaci** a **pravidla odchozí synchronizace** se spouštějí v pořadí podle priority od nižších po vyšší. Chcete-li zobrazit pravidla synchronizace, můžete přejít na **Editor synchronizačních pravidel** z aplikací klasické pracovní plochy. **Pravidla pro příchozí synchronizaci** přináší data z cs do MV. **Pravidla odchozí synchronizace** přesouvá data z MV do cs.

* Export do AD: po spuštění synchronizace se objekty exportují ze služby AD CS do **Active Directory**.

* Export do AAD: po spuštění synchronizace se objekty exportují z AAD CS do **Azure Active Directory**.

### <a name="step-by-step-investigation"></a>**Krok za krokem šetření**

* Začneme hledat v **úložišti Metaverse** a podíváme se na mapování atributů ze zdroje do cíle.

* Spusťte **Synchronization Service Manager** z aplikací klasické pracovní plochy, jak je znázorněno níže:

  ![Spustit Synchronization Service Manager](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/startmenu.png)

* V **Synchronization Service Manager**vyberte **hledání Metaverse**, vyberte **Rozsah podle typu objektu**, vyberte objekt pomocí atributu a klikněte na tlačítko **Hledat** .

  ![Hledání v úložišti Metaverse](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvsearch.png)

* Dvojím kliknutím na objekt, který najdete v **úložišti Metaverse** , zobrazte všechny jeho atributy. Kliknutím na kartu **konektory** se můžete podívat na odpovídající objekt ve všech **prostorech konektoru**.

  ![Konektory objektů úložiště metaverse](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvattributes.png)

* Dvojitým kliknutím na **konektor služby Active Directory** zobrazíte atributy **prostoru konektoru** . Klikněte na tlačítko **Náhled** . v následujícím dialogovém okně klikněte na tlačítko **vygenerovat náhled** .

  ![Snímek obrazovky zobrazující obrazovku vlastností objektu prostoru konektoru s zvýrazněným tlačítkem náhledu](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/csattributes.png)

* Nyní klikněte na **tok importovaného atributu**, který ukazuje tok atributů z **prostoru konektoru služby Active Directory do úložiště** **Metaverse**. Sloupec **pravidlo synchronizace** zobrazuje, které **pravidlo synchronizace** přispělo k tomuto atributu. Sloupec **zdroj dat** zobrazuje atributy z **prostoru konektoru**. Sloupec **atributu Metaverse** zobrazuje atributy v **úložišti Metaverse**. Zde můžete vyhledat atribut, který zde není synchronizován. Pokud zde atribut nenajdete, není to namapováno a je třeba vytvořit nové vlastní **synchronizační pravidlo** pro mapování atributu.

  ![Atributy prostoru konektoru](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/cstomvattributeflow.png)

* Kliknutím na **tok atributů exportu** v levém podokně zobrazíte tok atributů z úložiště **Metaverse** zpátky do **prostoru konektoru služby Active Directory** pomocí **pravidel odchozí synchronizace**.

  ![Snímek obrazovky, který zobrazuje tok atributů z úložiště metaverse zpátky do prostoru konektoru služby Active Directory pomocí pravidel odchozí synchronizace.](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvtocsattributeflow.png)

* Podobně můžete zobrazit objekt **prostoru konektoru Azure Active Directory** a můžete vygenerovat **Náhled** pro zobrazení toku atributů z úložiště **Metaverse** do **prostoru konektoru** a naopak, což vám umožní zjistit, proč se atribut nesynchronizuje.

## <a name="recommended-documents"></a>**Doporučené dokumenty**
* [Azure AD Connect synchronizace: technické koncepty](./how-to-connect-sync-technical-concepts.md)
* [Azure AD Connect synchronizace: Principy architektury](./concept-azure-ad-connect-sync-architecture.md)
* [Azure AD Connect synchronizace: principy deklarativního zřizování](./concept-azure-ad-connect-sync-declarative-provisioning.md)
* [Azure AD Connect synchronizace: principy deklarativních zřizovacích výrazů](./concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Synchronizace služby Azure AD Connect: Principy výchozí konfigurace](./concept-azure-ad-connect-sync-default-configuration.md)
* [Azure AD Connect synchronizace: principy uživatelů, skupin a kontaktů](./concept-azure-ad-connect-sync-user-and-contacts.md)
* [Azure AD Connect Sync: atributy stínů](./how-to-connect-syncservice-shadow-attributes.md)

## <a name="next-steps"></a>Další kroky

- [Azure AD Connect synchronizace](how-to-connect-sync-whatis.md).
- [Co je hybridní identita?](whatis-hybrid-identity.md).
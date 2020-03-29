---
title: Poradce při potížích s atributem, který není synchronizován ve službě Azure AD Connect | Dokumenty společnosti Microsoft
description: Toto téma obsahuje postup řešení problémů se synchronizací atributů pomocí úlohy řešení potíží.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a639b14c9313179816f6376aa0c5642a645ea344
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60455909"
---
# <a name="troubleshoot-an-attribute-not-synchronizing-in-azure-ad-connect"></a>Poradce při potížích s atributem, který není synchronizován ve službě Azure AD Connect

## <a name="recommended-steps"></a>**Doporučené kroky**

Než prozkoumáme problémy se synchronizací atributů, pochopme proces synchronizace **Služby Azure AD Connect:**

  ![Proces synchronizace připojení Azure AD](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologie**

* **CS:** Mezerník spojnice, tabulka v databázi.
* **MV:** Metaverse, tabulka v databázi.
* **AD:** Active Directory
* **AAD:** Azure Active Directory

### <a name="synchronization-steps"></a>**Kroky synchronizace**

* Import ze služby AD: Objekty služby Active Directory jsou přeneseny do služby AD CS.

* Import z aad: Objekty Služby Azure Active Directory jsou přeneseny do služby AAD CS.

* Synchronizace: **Pravidla příchozí synchronizace** a **Pravidla odchozí synchronizace** jsou spouštěna v pořadí podle čísla priority od nižší k vyšší. Chcete-li zobrazit pravidla synchronizace, můžete přejít na **Editor pravidel synchronizace** z aplikací klasické pracovní plochy. **Pravidla příchozí synchronizace** přináší data z CS do MV. **Pravidla odchozí synchronizace** přesouvá data z MV do CS.

* Export do služby AD: Po spuštění synchronizace jsou objekty exportovány ze služby AD CS do **služby Active Directory**.

* Export do služby AAD: Po spuštění synchronizace se objekty exportují z aad cs do **služby Azure Active Directory**.

### <a name="step-by-step-investigation"></a>**Krok za krokem vyšetřování**

* Začneme naše hledání z **Metaverse** a podívat se na mapování atributů ze zdroje na cíl.

* Spusťte **Správce synchronizačních služeb** z desktopových aplikací, jak je znázorněno níže:

  ![Spuštění Správce synchronizačních služeb](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/startmenu.png)

* Ve **Správci synchronizačních služeb**vyberte **hledání Metaverse**, vyberte **obor podle typu objektu**, vyberte objekt pomocí atributu a klepněte na tlačítko **Hledat.**

  ![Metaverse hledání](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvsearch.png)

* Poklepáním na objekt nalezený ve vyhledávání **Metaverse** zobrazíte všechny jeho atributy. Kliknutím na kartu **Konektory** se můžete podívat na odpovídající objekt ve všech **prostorech konektoru**.

  ![Konektory objektů Metaverse](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvattributes.png)

* Poklepáním na **konektor služby Active Directory** zobrazte atributy prostoru **konektoru.** Klikněte na tlačítko **Náhled,** v následujícím dialogu klikněte na **tlačítko Generovat náhled.**

  ![Atributy prostoru spojnice](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/csattributes.png)

* Nyní klikněte na **importovat atribut toku**, to ukazuje tok atributů z **Prostoru konektoru služby Active Directory** do **Metaverse**. Sloupec **Pravidlo synchronizace** zobrazuje, které **pravidlo synchronizace** přispělo k tomuto atributu. **Sloupec Zdroj dat** zobrazuje atributy z **prostoru konektoru**. Sloupec **Atribut Metaverse** zobrazuje atributy v **Metaverse**. Zde se můžete podívat na atribut, který se nesynchronizuje. Pokud zde atribut nenajdete, není to namapováno a musíte vytvořit nové vlastní **pravidlo synchronizace,** abyste atribut namapovali.

  ![Atributy prostoru spojnice](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/cstomvattributeflow.png)

* Kliknutím na **tok atributů Export** v levém podokně zobrazíte tok atributů z **Metaverse** zpět do **prostoru konektoru služby Active Directory** pomocí pravidel odchozí **synchronizace**.

  ![Atributy prostoru spojnice](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvtocsattributeflow.png)

* Podobně můžete zobrazit objekt **Azure Active Directory Connector Space** a můžete generovat **náhled** pro zobrazení toku atributů z **Metaverse** do **prostoru konektoru** a naopak, tímto způsobem můžete zjistit, proč se atribut nesynchronizuje.

## <a name="recommended-documents"></a>**Doporučené dokumenty**
* [Synchronizace služby Azure AD Connect: Technické koncepty](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-technical-concepts)
* [Synchronizace Azure AD Connect: Principy architektury](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture)
* [Synchronizace Azure AD Connect: Principy deklarativního zřizování](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning)
* [Synchronizace azure a připojení služby Azure AD: principy výrazů deklarativního zřizování](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning-expressions)
* [Synchronizace služby Azure AD Connect: Principy výchozí konfigurace](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-default-configuration)
* [Synchronizace azure ad připojení: principy uživatelů, skupin a kontaktů](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-user-and-contacts)
* [Synchronizace služby Azure AD Connect: Atributy Stín](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-syncservice-shadow-attributes)

## <a name="next-steps"></a>Další kroky

- [Synchronizace služby Azure AD Connect](how-to-connect-sync-whatis.md).
- [Co je hybridní identita?](whatis-hybrid-identity.md).

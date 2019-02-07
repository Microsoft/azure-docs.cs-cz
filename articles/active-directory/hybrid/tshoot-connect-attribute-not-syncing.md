---
title: Řešení potíží s atributem není synchronizace ve službě Azure AD Connect | Dokumentace Microsoftu
description: Toto téma popisuje kroky pro řešení potíží se synchronizací atribut pomocí úlohy řešení potíží.
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
ms.openlocfilehash: ef167866cb682cec71b618e972d3d402c8e050c9
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55773425"
---
# <a name="troubleshoot-an-attribute-not-synchronizing-in-azure-ad-connect"></a>Řešení potíží s atributem není synchronizace ve službě Azure AD Connect

## <a name="recommended-steps"></a>**Doporučené kroky**

Před zkoumáním atribut synchronizace problémy, Pojďme **Azure AD Connect** proces synchronizace:

  ![Procesu synchronizace pro Azure AD Connect](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologie**

* **CS:** Prostor konektoru, tabulku v databázi.
* **MV:** Úložiště Metaverse na tabulku v databázi.
* **AD:** Active Directory
* **AAD:** Azure Active Directory

### <a name="synchronization-steps"></a>**Koky synchronizace**

* Import ze služby AD: Objekty služby Active Directory jsou zařazení do systému AD CS.

* Import ze služby AAD: Azure Active Directory objekty jsou zařazení do systému AAD CS.

* Synchronizace: **Příchozí pravidla synchronizace** a **pravidla odchozí synchronizace** jsou spouštěny v pořadí podle priority číslo nižší vyšší. Chcete-li zobrazit synchronizační pravidla, můžete přejít na **Editor pravidel synchronizace** z desktopové aplikace. **Příchozí pravidla synchronizace** přináší v datech z CS MV. **Pravidla odchozí synchronizace** přesouvá data z MV ke službě CS.

* Export do AD: Po spuštění synchronizace, jsou exportovány objekty ze služby AD CS na **služby Active Directory**.

* Exportovat do AAD: Po spuštění synchronizace se objekty byly exportovány z CS AAD k **Azure Active Directory**.

### <a name="step-by-step-investigation"></a>**Krok za krokem šetření**

* Začneme naše vyhledávání z **Metaverse** a podívejte se na mapování atributů ze zdroje do cíle.

* Spuštění **Synchronization Service Manager** z desktopové aplikace, jak je znázorněno níže:

  ![Spustit Synchronization Service Manager](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/startmenu.png)

* Na **Synchronization Service Manager**, vyberte **vyhledávání Metaverse**vyberte **oboru podle typu objektu**, vyberte objekt pomocí atributu a klikněte na tlačítko **Hledání** tlačítko.

  ![Vyhledávání Metaverse](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvsearch.png)

* Dvakrát klikněte na objekt v nalezen **Metaverse** Hledat zobrazíte jeho atributy. Můžete kliknout na **konektory** kartu a podívejte se na odpovídající objekt ve všech **prostor konektoru**.

  ![Konektory objektu úložiště Metaverse](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvattributes.png)

* Dvakrát klikněte na **konektor služby Active Directory** zobrazíte **prostoru konektoru** atributy. Klikněte na **ve verzi Preview** na následující dialogové okno kliknutím na tlačítko **generovat ve verzi Preview** tlačítko.

  ![Atributy prostoru konektoru](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/csattributes.png)

* Nyní klikněte na **toku atributu importu**, ukazuje tok atributů z **prostoru konektoru Active Directory** k **Metaverse**. **Pravidlo synchronizace** sloupec zobrazuje, které **synchronizační pravidlo** přispívají k atributu. **Zdroj dat** sloupci se zobrazuje atributy z **prostoru konektoru**. **Atribut úložiště Metaverze** sloupci se zobrazuje atributy v **Metaverse**. Můžete vyhledat atribut nesynchronizuje tady. Pokud nenajdete atribut tady, a to není namapována a budete muset vytvořit nové vlastní **synchronizační pravidlo** mapovat atribut.

  ![Atributy prostoru konektoru](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/cstomvattributeflow.png)

* Klikněte na **toku atributů exportu** v levém podokně, chcete-li zobrazit tok atributů z **Metaverse** zpět **prostoru konektoru Active Directory** pomocí  **Pravidla odchozí synchronizace**.

  ![Atributy prostoru konektoru](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvtocsattributeflow.png)

* Podobně můžete zobrazit **prostoru konektoru Active Directory Azure** objekt a může generovat **ve verzi Preview** zobrazíte tok atributů z **Metaverse** k **Prostoru konektoru** a naopak, tímto způsobem můžete prozkoumat proč atribut nesynchronizuje.

## <a name="recommended-documents"></a>**Doporučené dokumenty**
* [Synchronizace Azure AD Connect: Technické koncepty](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-technical-concepts)
* [Synchronizace Azure AD Connect: Principy architektury](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture)
* [Synchronizace Azure AD Connect: Principy deklarativního zřizování](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning)
* [Synchronizace Azure AD Connect: Principy výrazů deklarativního zřizování](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning-expressions)
* [Synchronizace Azure AD Connect: Principy výchozí konfigurace](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-default-configuration)
* [Synchronizace Azure AD Connect: Principy uživatelů, skupin a kontaktů](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-user-and-contacts)
* [Synchronizace Azure AD Connect: Stínové atributy](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-syncservice-shadow-attributes)

## <a name="next-steps"></a>Další kroky

- [Synchronizace Azure AD Connect](how-to-connect-sync-whatis.md).
- [Co je hybridní identitu? ](whatis-hybrid-identity.md).

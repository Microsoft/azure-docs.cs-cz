---
title: 'Azure AD Connect synchronizace: pochopení a přizpůsobení synchronizace | Microsoft Docs'
description: Vysvětluje, jak Azure AD Connect synchronizace funguje a jak přizpůsobit.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ee4bf802-045b-4da0-986e-90aba2de58d6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/08/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cea26cb119f64679807bc6c5eaadb41b341e5d5a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89662387"
---
# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Azure AD Connect synchronizace: pochopení a přizpůsobení synchronizace
Služby synchronizace Azure Active Directory Connect (synchronizace Azure AD Connect) jsou hlavní součástí Azure AD Connect. Postará se o všechny operace, které souvisejí s synchronizací dat identity mezi místním prostředím a službou Azure AD. Azure AD Connect synchronizace je nástupce DirSync, Azure AD Sync a Forefront Identity Manageru s nakonfigurovaným konektorem Azure Active Directory.

Toto téma je domovkou pro **Azure AD Connect synchronizaci** (označuje se také jako **synchronizační modul**) a obsahuje odkazy na všechna ostatní témata, která s ním souvisejí. Odkazy na Azure AD Connect najdete v tématu [Integrace místních identit s Azure Active Directory](whatis-hybrid-identity.md).

Synchronizační služba se skládá ze dvou součástí: místní **Azure AD Connect synchronizační** součást a strana služby ve službě Azure AD s názvem **Azure AD Connect Sync Service**.

## <a name="azure-ad-connect-sync-topics"></a>Témata týkající se Azure AD Connect synchronizace
| Téma | Co pokrývá a kdy číst |
| --- | --- |
| **Azure AD Connect synchronizace – základy** | |
| [Principy architektury](concept-azure-ad-connect-sync-architecture.md) |Pro ty, které jsou pro synchronizační modul nové a chtějí se dozvědět informace o architektuře a používaných pojmech. |
| [Technické koncepty](how-to-connect-sync-technical-concepts.md) |Krátká verze tématu architektury a stručně vysvětluje použité výrazy. |
| [Topologie pro Azure AD Connect](plan-connect-topologies.md) |V této části najdete popis různých topologií a scénářů, které synchronizační modul podporuje. |
| **Vlastní konfigurace** | |
| [Znovu spustit Průvodce instalací](how-to-connect-installation-wizard.md) |Vysvětluje, jaké možnosti máte k dispozici po opětovném spuštění Průvodce instalací Azure AD Connect. |
| [Principy deklarativního zřizování](concept-azure-ad-connect-sync-declarative-provisioning.md) |Popisuje model konfigurace s názvem deklarativní zřizování. |
| [Principy výrazů deklarativního zřizování](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) |Popisuje syntaxi pro jazyk výrazů použitý při deklarativním zřizování. |
| [Principy výchozí konfigurace](concept-azure-ad-connect-sync-default-configuration.md) |Popisuje Přednastavená pravidla a výchozí konfiguraci. Popisuje také, jak budou pravidla spolupracovat pro scénáře, které jsou připravené k použití. |
| [Principy uživatelů a kontaktů](concept-azure-ad-connect-sync-user-and-contacts.md) |Pokračuje v předchozím tématu a popisuje, jak konfigurace uživatelů a kontaktů společně spolupracuje, zejména v prostředí s více doménovými strukturami. |
| [Postup provedení změny ve výchozí konfiguraci](how-to-connect-sync-change-the-configuration.md) |Provede vás postupem provedení běžné změny konfigurace toků atributů. |
| [Osvědčené postupy pro změnu výchozí konfigurace](how-to-connect-sync-best-practices-changing-default-configuration.md) |Omezení podpory a provádění změn v dopředných konfiguracích. |
| [Konfigurace filtrování](how-to-connect-sync-configure-filtering.md) |Popisuje různé možnosti, jak omezit, které objekty se synchronizují do služby Azure AD, a krok za krokem, jak tyto možnosti nakonfigurovat. |
| **Funkce a scénáře** | |
| [Prevence náhodného odstranění](how-to-connect-sync-feature-prevent-accidental-deletes.md) |Popisuje funkci *prevence náhodného odstranění* a jak ji nakonfigurovat. |
| [Scheduler](how-to-connect-sync-feature-scheduler.md) |Popisuje vestavěný Scheduler, který importuje, synchronizuje a exportuje data. |
| [Implementace synchronizace hodnot hash hesel](how-to-connect-password-hash-synchronization.md) |Popisuje, jak funguje synchronizace hesel, jak implementovat a jak vyvíjejí a řešit potíže. |
| [Zpětný zápis zařízení](how-to-connect-device-writeback.md) |Popisuje způsob, jakým funguje zpětný zápis zařízení v Azure AD Connect. |
| [Rozšíření adresáře](how-to-connect-sync-feature-directory-extensions.md) |V této části najdete popis postupu při rozšiřování schématu Azure AD pomocí vlastních atributů. |
| [Microsoft 365 PreferredDataLocation](how-to-connect-sync-feature-preferreddatalocation.md) |Popisuje, jak umístit Microsoft 365 prostředky uživatele do stejné oblasti jako uživatel. |
| **Synchronizační služba** | |
| [Funkce synchronizační služby Azure AD Connect](how-to-connect-syncservice-features.md) |Popisuje synchronizační službu a postup změny nastavení synchronizace ve službě Azure AD. |
| [Odolnost duplicitních atributů](how-to-connect-syncservice-duplicate-attribute-resiliency.md) |Popisuje, jak povolit a použít atributy **userPrincipalName** a **proxyAddresses** duplicitních hodnot atributů. |
| **Operace a uživatelské rozhraní** | |
| [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md) |Popisuje Synchronization Service Manager uživatelské rozhraní, včetně [operací](how-to-connect-sync-service-manager-ui-operations.md), [konektorů](how-to-connect-sync-service-manager-ui-connectors.md), [nástroje Metaverse Designer](how-to-connect-sync-service-manager-ui-mvdesigner.md)a karet [hledání v úložišti Metaverse](how-to-connect-sync-service-manager-ui-mvsearch.md) . |
| [Provozní úlohy a požadavky](./how-to-connect-sync-staging-server.md) |Popisuje provozní obavy, jako je například zotavení po havárii. |
| **Jak...** | |
| [Resetování účtu Azure AD](how-to-connect-azureadaccount.md) |Postup resetování přihlašovacích údajů účtu služby použitého pro připojení z Azure AD Connect synchronizaci do Azure AD. |
| **Další informace a odkazy** | |
| [Porty](reference-connect-ports.md) |Zobrazuje seznam portů, které je třeba otevřít mezi synchronizačním modulem a místními adresáři a službou Azure AD. |
| [Atributy synchronizované se službou Azure Active Directory](reference-connect-sync-attributes-synchronized.md) |Zobrazí seznam všech synchronizovaných atributů mezi místními službami AD a Azure AD. |
| [Reference k funkcím](reference-connect-sync-functions-reference.md) |Zobrazí seznam všech funkcí dostupných v deklarativních zřizováních. |

## <a name="additional-resources"></a>Další materiály
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)
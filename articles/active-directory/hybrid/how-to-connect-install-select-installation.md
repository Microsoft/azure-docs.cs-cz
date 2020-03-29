---
title: 'Azure AD Connect: Vyberte typ instalace | Dokumenty společnosti Microsoft'
description: Toto téma vás provede výběrem typu instalace, který se má použít pro Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90a624a6b3b4696899af0d8606f653df260cc201
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60348276"
---
# <a name="select-which-installation-type-to-use-for-azure-ad-connect"></a>Výběr typu instalace pro Azure AD Connect
Azure AD Connect má dva typy instalace pro novou instalaci: Express a přizpůsobené. Toto téma vám pomůže rozhodnout, kterou možnost použít během instalace.

## <a name="express"></a>Express
Express je nejběžnější volbou a používá ji přibližně 90 % všech nových instalací. Byl navržen tak, aby poskytoval konfiguraci, která funguje pro nejběžnější scénáře zákazníků.

Předpokládá:

- V místním prostředí máte jednu doménovou strukturu služby Active Directory.
- Máte účet správce rozlehlé sítě, který můžete použít pro instalaci.
- V místní službě Active Directory máte méně než 100 000 objektů.

Získáte:

- [Synchronizace hash hesla](how-to-connect-password-hash-synchronization.md) z místního do Azure AD pro jednotné přihlašování.
- Konfigurace, která synchronizuje [uživatele, skupiny, kontakty a počítače s Windows 10](concept-azure-ad-connect-sync-default-configuration.md).
- Synchronizace všech způsobilých objektů ve všech doménách a všech mimo usa.
- [Automatický upgrade](how-to-connect-install-automatic-upgrade.md) je povolen, abyste měli jistotu, že vždy používáte nejnovější dostupnou verzi.

Možnosti, kde můžete stále používat Express:

- Pokud nechcete synchronizovat všechny vou, můžete stále používat Express a na poslední stránce zrušit zaškrtnutí políčka **Spustit proces synchronizace...***. Potom znovu spusťte průvodce instalací a změňte hlavní uživatele v [možnostech konfigurace](how-to-connect-installation-wizard.md#customize-synchronization-options) a povolte naplánovanou synchronizaci.
- Chcete povolit jednu z funkcí v Azure AD Premium, jako je například zpětný zápis hesla. Nejprve projděte expresem, abyste dokončili počáteční instalaci. Potom znovu spusťte Průvodce instalací a změňte [možnosti konfigurace](how-to-connect-installation-wizard.md#customize-synchronization-options).

## <a name="custom"></a>Vlastní
Přizpůsobená cesta umožňuje mnohem více možností než express. Měl by být používán ve všech případech, kdy konfigurace popsaná v předchozí části express není reprezentativní pro vaši organizaci.

Použijte, když:

- Nemáte přístup k účtu správce rozlehlé sítě ve službě Active Directory.
- Máte více než jednu doménovou strukturu nebo plánujete v budoucnu synchronizovat více než jednu doménovou strukturu.
- Máte domény v doménové struktuře, které nejsou dostupné ze serveru Connect.
- Plánujete použít federační nebo předávací ověřování pro přihlášení uživatele.
- Máte více než 100 000 objektů a je třeba použít úplný SQL Server.
- Plánujete použít filtrování založené na skupině, nikoli pouze filtrování založené na doméně nebo ou.

## <a name="upgrade-from-dirsync"></a>Upgrade z nástroje DirSync
Pokud aktuálně používáte DirSync, postupujte podle pokynů v [inovaci z DirSync](how-to-dirsync-upgrade-get-started.md) upgradovat stávající konfiguraci. Existují dvě různé možnosti upgradu:

- Instalujte na místě a nainstalujte aplikaci Connect na stejný server.
- Paralelní nasazení pro instalaci připojit na nový server, zatímco stávající server DirSync je stále funkční.

## <a name="upgrade-from-azure-ad-sync"></a>Upgrade ze synchronizace Azure AD
Pokud aktuálně používáte Azure AD Sync, pak můžete postupovat [podle stejných kroků,](how-to-upgrade-previous-version.md) jako při upgradu z jedné verze připojení na novější. Existují dvě různé možnosti upgradu:

- Instalujte na místě a nainstalujte aplikaci Connect na stejný server.
- Migrace houpaček pro instalaci připojení na nový server, zatímco existující server Azure AD Sync je stále funkční.

## <a name="migrate-from-fim2010-or-mim2016"></a>Migrace z FIM2010 nebo MIM2016
Pokud aktuálně používáte Forefront Identity Manager 2010 nebo Microsoft Identity Manager 2016 s Konektor Azure AD, pak je dinou možností je migrace. Postupujte podle kroků popsaných v [swing-migrace](how-to-upgrade-previous-version.md#swing-migration). V postupech nahraďte jakoukoli zmínku o Azure AD Sync fim2010/MIM2016.

## <a name="next-steps"></a>Další kroky
V závislosti na možnosti, kterou jste zvolili, použijte tabulku obsahu vlevo a vyhledejte článek s podrobnými kroky.

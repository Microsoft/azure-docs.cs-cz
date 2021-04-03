---
title: 'Azure AD Connect: Vyberte typ instalace | Microsoft Docs'
description: Toto téma vás provede postupem výběru typu instalace, který se má použít pro Azure AD Connect
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
ms.topic: how-to
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7275d115210100bdd4a3a2eb683c867a6a4a4f4a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996606"
---
# <a name="select-which-installation-type-to-use-for-azure-ad-connect"></a>Výběr typu instalace pro Azure AD Connect
Azure AD Connect má dva typy instalace pro novou instalaci: Expresní a přizpůsobený. Toto téma vám pomůže rozhodnout se, kterou možnost použít při instalaci.

## <a name="express"></a>Express
Možnost expresní je nejběžnější a používá se přibližně 90% všech nových instalací. Byla navržena tak, aby poskytovala konfiguraci, která funguje v nejběžnějších scénářích pro zákazníky.

Předpokládá:

- Máte jednu místní doménovou strukturu služby Active Directory.
- Máte účet správce podnikové sítě, který můžete použít k instalaci.
- Máte méně než 100 000 objektů v místní službě Active Directory.

Získáte:

- [Synchronizace hodnot hash hesel](how-to-connect-password-hash-synchronization.md) z místního prostředí do Azure AD pro jednotné přihlašování
- Konfigurace, která synchronizuje [uživatele, skupiny, kontakty a počítače s Windows 10](concept-azure-ad-connect-sync-default-configuration.md).
- Synchronizace všech oprávněných objektů ve všech doménách a všech organizačních jednotkách.
- [Automatický upgrade](how-to-connect-install-automatic-upgrade.md) je povolený, aby se zajistilo, že vždy používáte nejnovější dostupnou verzi.

Možnosti, kde můžete dál používat Express:

- Pokud nechcete synchronizovat všechny organizační jednotky, můžete i nadále používat expresní a na poslední stránce, zrušit výběr * * spustit proces synchronizace... * * *. Pak znovu spusťte Průvodce instalací a změňte oj v [Možnosti konfigurace](how-to-connect-installation-wizard.md#customize-synchronization-options) a povolte naplánovanou synchronizaci.
- Chcete povolit jednu z funkcí v Azure AD Premium, jako je třeba zpětný zápis hesla. Nejprve Projděte Express, abyste získali počáteční instalaci. Pak znovu spusťte Průvodce instalací a změňte [Možnosti konfigurace](how-to-connect-installation-wizard.md#customize-synchronization-options).

## <a name="custom"></a>Vlastní
Přizpůsobená cesta umožňuje mnoho dalších možností než expresní. Měl by se používat ve všech případech, kdy konfigurace popsaná v předchozím oddílu pro Express není pro vaši organizaci reprezentativní.

Použijte, když:

- Nemáte přístup k účtu podnikového správce ve službě Active Directory.
- Máte více než jednu doménovou strukturu nebo plánujete synchronizaci více než jedné doménové struktury v budoucnu.
- Máte domény v doménové struktuře nedosažitelné ze serveru Connect.
- Plánujete použít federaci nebo předávací ověřování pro přihlášení uživatele.
- Máte více než 100 000 objektů a potřebujete použít kompletní SQL Server.
- Plánujete použití filtrování založeného na skupinách a nikoli jenom filtrování založeného na doméně nebo organizační jednotce.

## <a name="upgrade-from-dirsync"></a>Upgrade z nástroje DirSync
Pokud aktuálně používáte DirSync, postupujte podle kroků v části [upgrade z DirSync](how-to-dirsync-upgrade-get-started.md) na upgrade stávající konfigurace. K dispozici jsou dvě různé možnosti upgradu:

- Místní upgrade pro instalaci nástroje Connect na stejném serveru.
- Paralelní nasazení pro instalaci připojení na nový server, zatímco stávající server DirSync je stále v provozu.

## <a name="upgrade-from-azure-ad-sync"></a>Upgrade z Azure AD Sync
Pokud aktuálně používáte Azure AD Sync, můžete postupovat [stejným způsobem](how-to-upgrade-previous-version.md) jako při upgradu z jedné verze připojení na novější. K dispozici jsou dvě různé možnosti upgradu:

- Místní upgrade pro instalaci nástroje Connect na stejném serveru.
- Pokud je stávající server Azure AD Sync stále funkční, je potřeba provést migraci k instalaci připojení na nový server.

## <a name="migrate-from-fim2010-or-mim2016"></a>Migrace z FIM2010 nebo V MIM2016
Pokud aktuálně používáte Forefront Identity Manager 2010 nebo Microsoft Identity Manager 2016 s konektorem Azure AD, pak je jedinou možností migrace. Postupujte podle kroků popsaných v tématu Postup [– migrace](how-to-upgrade-previous-version.md#swing-migration). V tomto postupu nahraďte všechny zmínky o Azure AD Sync FIM2010/V MIM2016.

## <a name="next-steps"></a>Další kroky
V závislosti na možnosti, kterou jste vybrali k použití, použijte tabulku obsahu vlevo a vyhledejte článek s podrobnými kroky.

---
title: Microsoft Threat Modeling Tool verze 4/9/2019
titleSuffix: Azure
description: Dokumentace k verzi nástroje pro modelování hrozeb verze 7.1.60408.1.
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 69dd2c6cdba41779849b4eb6b889cde9b1d6e5c9
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913564"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>Threat Modeling Tool vydání aktualizace 7.1.60408.1-4/9/2019

7.1.60408.1 verze Microsoft Threat Modeling Tool (TMT) byla vydaná v dubnu 9 2019 a obsahuje následující změny:

- Nové vzorníky pro Azure Key Vault a Azure Traffic Manager
- Číslo verze TMT se teď zobrazuje na domovské obrazovce.
- Odkazy na podporu byly aktualizovány
- Opravy chyb

## <a name="feature-changes"></a>Změny funkcí

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Nové vzorníky pro Azure Key Vault a Azure Traffic Manager

![Snímek obrazovky zobrazuje ikony pro Azure Key Vault a Azure Traffic Manager.](./media/threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

Nové vzorníky a hrozby pro Azure Key Vault a Azure Traffic Manager byly přidány do sady vzorníků Azure. Při otevírání modelů založených na sadě vzorníků Azure se uživatelům zobrazí výzva k aktualizaci šablony přidružené k modelu. Aktualizace modelu založeného na sadě vzorníků Azure se dá spustit taky ručně pomocí příkazu použít šablonu v nabídce soubor a opětovným použitím nejnovějšího souboru Cloud Services. tb7 Azure.

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>Číslo verze TMT se teď zobrazuje na domovské obrazovce.

Verze Threat Modeling Tool klienta se teď zobrazuje na domovské obrazovce aplikace pro usnadnění přístupu.

![Snímek obrazovky zobrazuje Microsoft Threat Modeling Tool s číslem verze klienta.](./media/threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>Odkazy na podporu byly aktualizovány

Všechny odkazy podpory v rámci nástroje byly aktualizovány, aby bylo možné směrovat uživatele [tmtextsupport@microsoft.com](mailto:tmtextsupport@microsoft.com) spíše než Fórum MSDN.

## <a name="system-requirements"></a>Požadavky na systém

- Podporované operační systémy
  - [Microsoft Windows 10 – aktualizace pro výročí](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) nebo novější
- Vyžaduje se verze .NET.
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) nebo novější
- Další požadavky
  - K získání aktualizací nástroje a také šablon je vyžadováno připojení k Internetu.

## <a name="documentation-and-feedback"></a>Dokumentace a zpětná vazba

- Dokumentace k Threat Modeling Tool je umístěna na [docs.Microsoft.com](threat-modeling-tool.md)a obsahuje informace [o použití nástroje](threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Další kroky

Stáhněte si nejnovější verzi [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).

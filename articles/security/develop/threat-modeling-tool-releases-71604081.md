---
title: Vydání nástroje Microsoft Threat Modeling Tool 4/9/2019
titleSuffix: Azure
description: Dokumentace poznámek k verzi pro nástroj modelování hrozeb
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 59d385ba7de5bf7bceae4dc8ddadbca813046094
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78269714"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>Aktualizace nástroje pro modelování hrozeb 7.1.60408.1 - 4/9/2019

Verze 7.1.60408.1 nástroje Microsoft Threat Modeling Tool (TMT) byla vydána v dubnu 9 2019 a obsahuje následující změny:

- Nové šablony pro Azure Key Vault a Azure Traffic Manager
- Číslo verze TMT je nyní zobrazeno na domovské obrazovce
- Odkazy na podporu byly aktualizovány
- Opravy chyb

## <a name="feature-changes"></a>Změny funkcí

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Nové šablony pro Azure Key Vault a Azure Traffic Manager

![Vzorník trezoru klíčů Azure](./media/threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

Do sady vzorníků Azure byly přidány nové vzorníky a hrozby pro Azure Key Vault a Azure Traffic Manager. Při otevírání modelů na základě sady vzorníků Azure budou uživatelé vyzváni k aktualizaci šablony přidružené k modelu. Aktualizace modelu na základě sady vzorníků Azure lze také ručně iniciovat pomocí příkazu Použít šablonu v nabídce Soubor a opětovným použitím nejnovějšího souboru Azure Cloud Services.tb7.

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>Číslo verze TMT je nyní zobrazeno na domovské obrazovce

Klientská verze nástroje pro modelování hrozeb je nyní zobrazena na domovské obrazovce aplikace pro snadný přístup.

![Vzorník trezoru klíčů Azure](./media/threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>Odkazy na podporu byly aktualizovány

Všechny odkazy podpory v rámci nástroje byly [tmtextsupport@microsoft.com](mailto:tmtextsupport@microsoft.com) aktualizovány tak, aby uživatele nasměrovaly spíše na fórum MSDN.

## <a name="system-requirements"></a>Požadavky na systém

- Podporované operační systémy
  - [Aktualizace Microsoft Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) nebo novější
- Je vyžadována verze rozhraní .NET.
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) nebo novější
- Další požadavky
  - K přijímání aktualizací nástroje i šablon je nutné připojení k Internetu.

## <a name="documentation-and-feedback"></a>Dokumentace a zpětná vazba

- Dokumentace k nástroji pro modelování hrozeb je umístěna na [docs.microsoft.com](threat-modeling-tool.md)a obsahuje informace [o jeho používání](threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Další kroky

Stáhněte si nejnovější verzi [nástroje Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).

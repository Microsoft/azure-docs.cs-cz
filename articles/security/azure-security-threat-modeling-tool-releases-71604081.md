---
title: Nástroj pro modelování hrozeb uvolní – modelování nástroj Microsoft Threat – Azure | Dokumentace Microsoftu
description: Dokumentace v poznámkách k verzi nástroje Modelování ohrožení
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: jegeib
ms.openlocfilehash: 502c1e8a422eb9e74586e5a6820d5b12ec4ae2a4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798243"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>Vydání aktualizace nástroj pro modelování hrozeb 7.1.60408.1 - 4/9/2019

Verze 7.1.60408.1 z Microsoft Threat modelování nástroj (TMT) byla vydána 2019 9. dubna a obsahuje následující změny:

- Nové vzorníky pro Azure Key Vault a Azure Traffic Manageru
- Na domovské obrazovce se teď zobrazují číslo verze TMT
- Odkazy na podporu se aktualizovaly.
- Opravy chyb

## <a name="feature-changes"></a>Funkce změny

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Nové vzorníky pro Azure Key Vault a Azure Traffic Manageru

![Azure Key Vault vzorníku](./media/azure-security-threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

Na sadu vzorníku Azure byly přidány nové vzorníky a hrozeb pro Azure Key Vault a Azure Traffic Manageru. Při otevírání modelů na základě sady Azure vzorníku, uživatelům se výzva k aktualizaci šablony související s modelem. Aktualizuje se model založený na sadu vzorníku Azure můžete také ručně spustit pomocí příkazu "Použít šablonu" v nabídce "File" a opětovné použití nejnovější soubor Services.tb7 cloudu Azure.

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>Na domovské obrazovce se teď zobrazují číslo verze TMT

Verze klienta nástroje pro modelování hrozeb se teď zobrazují na domovské obrazovce aplikace pro usnadnění přístupu.

![Azure Key Vault vzorníku](./media/azure-security-threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>Odkazy na podporu se aktualizovaly.

Uživatelům s přímým přístupem tak, aby se aktualizovaly všechny odkazy na podporu v rámci nástroje [ tmtextsupport@microsoft.com ](mailto:tmtextsupport@microsoft.com) místo fórum na webu MSDN.

## <a name="system-requirements"></a>Systémové požadavky

- Podporované operační systémy
  - [Microsoft Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) nebo novější
- Požadovaná verze rozhraní .NET
  - [.NET 4.7.1](http://go.microsoft.com/fwlink/?LinkId=863262) nebo novější
- Další požadavky
  - Připojení k Internetu je potřebný k přijetí aktualizací nástroje, jakož i šablon.

## <a name="documentation-and-feedback"></a>Dokumentace ke službě a zpětná vazba

- Dokumentace pro nástroj pro modelování hrozeb se nachází na [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)a obsahuje informace o [o použití nástroje](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Další postup

Stáhněte si nejnovější verzi [nástroj pro modelování hrozeb Microsoftu](https://aka.ms/threatmodelingtool).
---
title: Azure App Service pro podporu modulu Runtime Java Windows
description: Toto téma obsahuje modul Runtime Java prohlášení k podpoře pro Azure App Service ve Windows.
author: bmitchell287
ms.author: brendm; joshuapa
ms.date: 3/22/2019
ms.topic: article
ms.service: app-service
manager: dougE
ms.openlocfilehash: ab0e9b9f272108745b629d2cb284f6c5ab52c76f
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522956"
---
# <a name="java-runtime-statement-of-support-for-app-service-on-windows"></a>Java runtime prohlášení k podpoře pro službu App Service ve Windows

## <a name="jdk-versions-and-maintenance"></a>JDK verze a údržba

Azure podporovaná Java Development Kit (JDK) je [Zulu](https://www.azul.com/downloads/azure-only/zulu/) zajišťována [Azul Systems](https://www.azul.com/).

Hlavní verze aktualizace bude poskytovat prostřednictvím nové možnosti modulu runtime ve službě Azure App Service pro Windows. Zákazníci, aktualizujte na tyto novější verze Javy podle konfigurace nasazení služby App Service a jsou zodpovědné za testování a zajištění hlavní aktualizace vyhovují jejich potřebám.

Podporované JDK jsou automaticky opravit čtvrtletně v lednu, dubnu, červenci a říjnu každý rok.

## <a name="security-updates"></a>Aktualizace zabezpečení

Opravy chyb a opravy pro chyby zabezpečení hlavní vydá Jakmile budou k dispozici od Azul Systems. "Hlavní" ohrožení zabezpečení je definována základní skóre 9.0 nebo vyšší na [NIST běžné ohrožení zabezpečení hodnocení systému, verze 2](https://nvd.nist.gov/cvss.cfm).

## <a name="deprecation-and-retirement"></a>Vyřazení a vyřazení z provozu

Pokud podporovaného modulu runtime Java se vyřadí z provozu, vývojáře v Azure pomocí ovlivněné runtime dostanou oznámení o zastarání aspoň šest měsíců před modul runtime byl vyřazen z provozu.

## <a name="local-development"></a>Místní vývoj

Vývojáři mohou stáhnout produkční verzi z Azul Zulu Enterprise sadu JDK pro místní vývoj z [serveru pro stahování společnosti Azul](https://www.azul.com/downloads/azure-only/zulu/).

## <a name="development-support"></a>Podpora vývoje

Odborná pomoc pro [podporovaných systémem Azure Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) je dostupné prostřednictvím společnosti Microsoft při vývoji pro Azure nebo [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) s [kvalifikovaný plán podpory Azure](https://azure.microsoft.com/support/plans/).

## <a name="runtime-support"></a>Podpora modulu CLR

Mohou vývojáři [otevřete problém](/azure/azure-supportability/how-to-create-azure-support-request) s JDK Azul Zulu prostřednictvím podpory Azure, pokud mají [plán podpory kvalifikovaný](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Další postup

Toto téma obsahuje modul Runtime Java prohlášení k podpoře pro Azure App Service ve Windows.
- Další informace o hostování webových aplikací pomocí služby Azure App Service najdete v tématu [Přehled služby App Service](overview.md).
- Informace o jazyce Java v Azure – vývoj naleznete v tématu [Azure pro vývojáře Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable).

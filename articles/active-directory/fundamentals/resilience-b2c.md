---
title: Odolnost sestavení při správě identit a přístupu zákazníků pomocí Azure AD B2C | Microsoft Docs
description: Metody pro sestavování odolnosti při správě identit a přístupu zákazníků pomocí Azure AD B2C
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b40918db03c260f899c36d306c892b787cc6371c
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724887"
---
# <a name="build-resilience-in-your-customer-identity-and-access-management-with-azure-active-directory-b2c"></a>Zajištění odolnosti při správě identit a přístupu zákazníků pomocí Azure Active Directory B2C

[Azure Active Directory (AD) B2C](../../active-directory-b2c/overview.md) je platforma pro správu zákaznických identit a přístupu (CIAM), která je navržená tak, aby vám pomohla úspěšně spustit nejdůležitější aplikace pro zákazníky. Máme spoustu integrovaných funkcí pro zajištění [odolnosti](https://azure.microsoft.com/blog/advancing-azure-active-directory-availability/) , které jsou navržené tak, aby vám pomohly škálovat naše potřeby a vylepšili odolnost v situaci, kdy dojde k potenciálním výpadkům. Kromě toho je důležité vzít v úvahu různé prvky návrhu a konfigurace v aplikaci a také způsob konfigurace aplikace v rámci Azure AD B2C, abyste měli jistotu, že máte odolné chování v reakci na scénáře výpadku nebo selhání. V tomto článku se podíváme na některé z osvědčených postupů, které vám pomůžou zvýšit odolnost.

Odolná služba je ta, která dál funguje bez přerušení. Zvýšení odolnosti ve službě můžete zvýšit pomocí:

- porozumění všem součástem

- vyloučení selhání v jednom bodě

- izolace neúspěšných komponent, aby se omezil jejich dopad

- zajištění redundance pomocí mechanismů rychlého převzetí služeb při selhání a cest obnovení

Při vývoji aplikace doporučujeme zvážit, jak [zvýšit odolnost ověřování a autorizace ve vašich aplikacích](resilience-app-development-overview.md) pomocí komponent identity vašeho řešení. Tento článek se snaží vyřešit vylepšení pro odolnost specifická pro Azure AD B2C aplikací. Naše doporučení se seskupují podle funkcí CIAM.

![Obrázek ukazuje komponenty CIAM ](media/resilience-b2c/high-level-components.png) v následujících částech, abychom vám pomohli sestavovat odolnost v následujících oblastech:

- [Činnost koncového uživatele](resilient-end-user-experience.md): pro tok ověřování můžete povolit záložní plán a zmírnit potenciální dopad na narušení služby Azure AD B2C Authentication Service.

- [Rozhraní s externími procesy](resilient-external-processes.md): odolnost sestavení v aplikacích a rozhraních pomocí obnovení z chyb.  

- [Osvědčené postupy pro vývojáře](resilience-b2c-developer-best-practices.md): Vyhněte se neopatrnosti kvůli běžným problémům s vlastními zásadami a zlepšení zpracování chyb v oblastech, jako jsou interakce s ověřovateli deklarací identity, aplikacemi třetích stran a rozhraními REST API.

- [Monitorování a analýza](resilience-with-monitoring-alerting.md): posuzuje stav služby monitorováním klíčových ukazatelů a detekcí selhání a výpadků výkonu prostřednictvím upozorňování.

- [Odolnost sestavení v infrastruktuře ověřování](resilience-in-infrastructure.md)

- [Zvýšení odolnosti při ověřování a autorizaci ve vašich aplikacích](resilience-app-development-overview.md)

Podívejte se na toto video a zjistěte, jak vytvářet odolné a škálovatelné toky pomocí Azure AD B2C.
>[!Video https://www.youtube.com/embed/8f_Ozpw9yTs]

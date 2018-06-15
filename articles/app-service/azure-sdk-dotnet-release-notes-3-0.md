---
title: Azure SDK pro rozhraní .NET 3.0 poznámky k verzi | Microsoft Docs
description: Poznámky k verzi sady Azure SDK pro rozhraní .NET 3.0
services: app-service\web
documentationcenter: .net
author: chrissfanos
editor: ''
ms.assetid: c83d815b-fc19-4260-821e-7d2a7206dffc
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 03/07/2017
ms.author: juliako
ms.openlocfilehash: 207aa137b25e44baf73e7f481ebc8b6362dfa245
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
ms.locfileid: "30162090"
---
# <a name="azure-sdk-for-net-30-release-notes"></a>Poznámky k verzi sady Azure SDK pro rozhraní .NET 3.0

Toto téma obsahuje poznámky k verzi pro verzi 3.0 sady Azure SDK pro .NET.

## <a name="azure-sdk-for-net-30-release-summary"></a>Azure SDK pro verzi rozhraní .NET 3.0 souhrn

Datum vydání: 03/07/2017
 
V této verzi byly zavedeny žádné nejnovější změny do Azure SDK 3.0. Je také žádné procesu upgradu potřeby využít tuto sadu SDK s existující projekty cloudové služby. Povolit použití Azure SDK 3.0 bez nutnosti upgradu, Azure SDK 3.0 nainstaluje do stejných adresářích jako sadu Azure SDK 2.9. Většina komponenty nezměnila hlavní verzi z 2.9, ale místo toho právě aktualizovat číslo sestavení.

## <a name="visual-studio-2017-rtw"></a>Visual Studio 2017 RTW

- V aplikaci Visual Studio 2017 tato verze sady Azure SDK pro .NET je založená na pracovního vytížení Azure. Všechny nástroje, které musíte udělat Azure development budou součástí Visual Studio 2017 do budoucna. Pro Visual Studio 2015 SDK bude stále k dispozici prostřednictvím WebPI. Azure SDK pro .NET verze jsme mít nepoužívá pro Visual Studio 2013 teď, když byla vydána Visual Studio 2017.

### <a name="azure-diagnostics"></a>Diagnostika Azure

- Změnit chování pouze ukládat částečné připojovací řetězec s klíčem nahrazuje token pro připojovací řetězec úložiště diagnostiky cloudové služby. Klíč skutečné úložiště jsou teď uložená v složce profilu uživatele, přístup se dá řídit. Visual Studio bude číst ze složky profilu uživatele pro místní ladění a proces publikování klíče úložiště. 
- V reakci na změnu popsané výše Visual Studio Online team rozšířené šablony úloh nasazení Azure Cloud Services, mohou uživatelé zadat klíč úložiště pro nastavení rozšíření diagnostiky při publikování do Azure na průběžnou integraci a nasazení.
- Jsme provedli jsme je umožněno ukládání zabezpečené připojovací řetězec a tokenizaci pro Azure Diagnostics (WAD), které vám pomůžou vyřešit problémy s konfigurací napříč environements.
 
### <a name="windows-server-2016-virtual-machines"></a>Windows Server 2016 virtuální počítače

- Visual Studio teď podporuje nasazení cloudové služby na virtuální počítače operačního systému rodiny 5 (Windows Server 2016). Pro existující cloudové služby můžete změnit nastavení pro nové řada operačního systému. Při vytváření nových cloudových služeb, pokud zvolíte možnost vytvoření služby pomocí rozhraní .net 4.6 nebo vyšší, bude použita výchozí služba používají operačního systému rodiny 5.  Další informace najdete [skupina hostovaných operačních systémů podporují tabulky](../cloud-services/cloud-services-guestos-update-matrix.md).

### <a name="known-issues"></a>Známé problémy

- Azure .NET SDK 3.0 zavedla problém při odebírání Visual Studio 2017 v konfiguraci vedle sebe s Visual Studiem 2015.  Pokud máte sadu Azure SDK pro Visual Studio 2015 nainstalována, emulátor úložiště Microsoft Azure a Microsoft Azure výpočetní emulátor budou odebrány, pokud odinstalujete Visual Studio 2017.  To způsobí chybu při vytváření a ladění nové projekty cloudových služeb v sadě Visual Studio 2015. Chcete-li tento problém vyřešit, znovu nainstalujte sadu Azure SDK z webové platformy.  Tento problém bude vyřešen v budoucí aktualizaci Visual Studio 2017.  .

 
### <a name="azure-in-role-cache"></a>Azure In-Role Cache 

- Podpora pro mezipaměť hostovaná v instanci Role Azure skončila 30. listopadu 2016. Další podrobnosti získáte kliknutím na [zde](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/).





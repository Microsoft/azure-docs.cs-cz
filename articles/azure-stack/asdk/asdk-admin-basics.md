---
title: Azure zásobníku Development Kit základy | Microsoft Docs
description: Popisuje, jak provádět základní správu pro Azure zásobníku Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: fa2ee4df9a0a28c08237f30fcf6a4bac50c21100
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34849504"
---
# <a name="asdk-administration-basics"></a>Základy správy ASDK 
Existuje několik věcí, které potřebujete vědět, pokud začínáte správy Azure zásobníku Development Kit (ASDK). V tomto návodu obsahuje přehled role jako operátor Azure zásobníku v prostředí pro testování a jak ověřit testovací uživatele může být produktivní rychle.

Nejdřív byste měli zkontrolovat [co je Azure zásobníku Development Kit?](asdk-what-is.md) článek a ujistěte se, pochopit účel ASDK a její omezení. Měli byste použít development kit jako "izolovaného", kde může vyhodnotit zásobník Azure pro vývoj a testování vaší aplikace v testovacím prostředí. 

Jako je například Azure Azure zásobníku innovates rychle, jsme budete pravidelně sestavení pro vydání nové služby ASDK. Však nelze upgradovat ASDK jako můžete zásobník Azure integrované systémy nasazení. Ano, pokud chcete přesunout na nejnovější verzi, musíte úplně [znovu nasaďte ASDK](asdk-redeploy.md). Nelze použít balíčky aktualizací. Tento proces trvá určitou dobu, ale výhodou je, že můžete vyzkoušet nejnovější funkce Jakmile budou k dispozici. 

## <a name="what-account-should-i-use"></a>Jaký účet mám použít?
Existuje několik, které byste měli vědět při správě Azure zásobníku důležité informace o účtu. Zejména v nasazení používáte Windows Server Active Directory Federation Services (AD FS) jako zprostředkovatele identity místo Azure Active Directory (Azure AD). Zásobník Azure integrované systémy a ASDK nasazení, platí následující aspekty účet:

|Účet|Azure AD|AD FS|
|-----|-----|-----|
|Místní správce (. \Administrator)|Správce hostitele ASDK|Správce hostitele ASDK|
|AzureStack\AzureStackAdmin|Správce hostitele ASDK<br><br>Lze použít pro přihlášení k portálu pro správu Azure zásobníku<br><br>Přístup k zobrazení a správě okruhy Service Fabric|Správce hostitele ASDK<br><br>Žádný přístup k portálu pro správu Azure zásobníku<br><br>Přístup k zobrazení a správě okruhy Service Fabric<br><br>Už vlastníkem služby výchozí zprostředkovatel předplatné (distribučních bodů)|
|AzureStack\CloudAdmin|Můžete používat a spuštění povolených příkazů v rámci privilegované koncový bod|Můžete používat a spuštění povolených příkazů v rámci privilegované koncový bod<br><br>Nemůže přihlásit k hostiteli ASDK<br><br>Vlastník předplatného poskytovatele výchozí (distribučních bodů)|
|Globální správce Azure AD|Použít během instalace<br><br>Vlastník předplatného poskytovatele výchozí (distribučních bodů)|Neuvedeno|
|

## <a name="what-tools-do-i-use-to-manage"></a>Jaké nástroje lze použít ke správě?
Můžete použít [zásobníku správce portálu Azure](https://adminportal.local.azurestack.external) nebo prostředí PowerShell pro správu Azure zásobníku. Nejjednodušší způsob, jak Seznamte se základními koncepty je prostřednictvím portálu. Pokud chcete pomocí prostředí PowerShell, budete muset nainstalovat [prostředí PowerShell pro Azure zásobníku](asdk-post-deploy.md#install-azure-stack-powershell) a [stažení nástroje Azure zásobníku z Githubu](asdk-post-deploy.md#download-the-azure-stack-tools).

Azure zásobníku používá jako podkladový mechanismus nasazení, správu a organizace Azure Resource Manager. Pokud se chystáte spravovat Azure zásobníku a podporu pro uživatele, měli byste získat informace o službě Správce prostředků Azure. Další informace načtením [Začínáme s Azure Resource Manager dokument White Paper](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf).

## <a name="your-typical-responsibilities"></a>Vaše odpovědnosti typické
Vaši uživatelé chtějí používat služby. Z perspektivy je vaše hlavní role Chcete-li tyto služby jsou pro ně dostupné. Pomocí ASDK, další služby, které nabízí, a jak provádět ty služeb dostupné podle [vytváření plánů, nabídky a kvóty](asdk-offer-services.md). Také budete muset přidat položky do marketplace, například bitové kopie virtuálních počítačů. Nejjednodušším způsobem je [stažení položky marketplace](asdk-marketplace-item.md) z Azure do Azure zásobníku.

> [!NOTE]
> Pokud chcete testovat vaše plány, nabídky a služby, měli byste použít [portálu user portal](https://portal.local.azurestack.external); není [portálu správce](https://adminportal.local.azurestack.external).

Kromě poskytování služeb, je nutné provést všechny regulární povinností operátoru zásobník Azure zachovat ASDK spuštěná. Tyto činnosti patří třeba následující:
- Přidání uživatelských účtů pro Azure Active Directory (Azure AD) nebo nasazení služby Active Directory Federation Services (AD FS).
- Přiřadit přístup na základě rolí role řízení (RBAC) (Toto není omezen na právě správci)
- Monitorování stavu infrastruktury
- Spravovat prostředky sítě a úložiště
- Nahradit hardware počítače hostitele selhání development kit 

## <a name="where-to-get-support"></a>Kde získat podporu
Pro development kit je klást otázky týkající se podpory ve vaší jedinou možností podpory [fórum Microsoft Azure zásobníku](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). Klikněte na ikonu nápovědy a podpory (otazník) v pravém horním rohu portálu správce, a pak klikněte na tlačítko **nová žádost o podporu**, přímo otevře webu fóra. Tato fóra jsou pravidelně sledovat. 

> [!IMPORTANT]
> ASDK je zkušební prostředí, a proto není žádná oficiální podporu nabízených prostřednictvím Microsoft podporu služby zákazníkům (CSS).

## <a name="next-steps"></a>Další postup
[Nasazení ASDK](asdk-install.md)


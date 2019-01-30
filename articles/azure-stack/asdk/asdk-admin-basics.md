---
title: Azure Stack Development Kit základy | Dokumentace Microsoftu
description: Popisuje, jak provádět základní správu pro Azure Stack Development Kit (ASDK).
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
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 79e031226f11d45121827795c34b0103235d8d79
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55248159"
---
# <a name="asdk-administration-basics"></a>Základy správy ASDK 
Existuje několik věcí, které je potřeba vědět, pokud je pro vás nová pro správu služby Azure Stack Development Kit (ASDK). Tento návod obsahuje základní informace o vaší role jako jejich operátory Azure stacku v prostředí pro testování a jak zajistit testovací uživatele může být tak produktivní rychle.

Nejprve si můžete projít [co je Azure Stack Development Kit?](asdk-what-is.md) článek a ujistěte se, že rozumíte účel ASDK a jaká jsou její omezení. Vývojová sada používejte jako "sandboxu", ve kterém můžete vyhodnotit Azure Stack pro vývoj a testování vaší aplikace v neprodukčním prostředí. 

Jako je Azure Azure Stack innovates rychle tak pravidelně vydáváme nové buildy ASDK. Nelze však aktualizovat ASDK jako nasazení Azure Stack integrované systémy. Ano, pokud chcete přesunout na nejnovější verzi, je nutné zcela [opětovné nasazení ASDK](asdk-redeploy.md). Balíčky aktualizací nelze použít. Tento proces trvá určitou dobu, ale výhodou je, že budete moct vyzkoušet nejnovější funkce ihned, jakmile budou k dispozici. 

## <a name="what-account-should-i-use"></a>Jaký účet mám použít?
Existuje několik důležitých informací účtu, které byste měli vědět při správě služby Azure Stack. Zejména v nasazeních pomocí systému Windows Server Active Directory Federation Services (AD FS) jako poskytovatel identit Azure Active Directory (Azure AD). Integrované systémy Azure Stack a ASDK nasazení, platí následující aspekty účtu:

|Účet|Azure AD|AD FS|
|-----|-----|-----|
|Místní správce (. \Administrator)|Správce ASDK hostitele|Správce ASDK hostitele|
|AzureStack\AzureStackAdmin|Správce ASDK hostitele<br><br>Můžete použít k přihlášení na portál pro správu služby Azure Stack<br><br>Přístup k zobrazení a správě okruhů Service Fabric|Správce ASDK hostitele<br><br>Žádný přístup k portálu pro správu služby Azure Stack<br><br>Přístup k zobrazení a správě okruhů Service Fabric<br><br>Už vlastníka z výchozí zprostředkovatel předplatného (službou Device Provisioning)|
|AzureStack\CloudAdmin|Můžete používat a povolených příkazů v rámci privilegovaných koncového bodu|Můžete používat a povolených příkazů v rámci privilegovaných koncového bodu<br><br>Přihlásit k hostiteli ASDK<br><br>Výchozí zprostředkovatel předplatného (DPS)|
|Globální správce Azure AD|Použít během instalace<br><br>Výchozí zprostředkovatel předplatného (DPS)|Neuvedeno|
|

## <a name="what-tools-do-i-use-to-manage"></a>Jaké nástroje použít ke správě?
Můžete použít [portálu Azure Stack správce](https://adminportal.local.azurestack.external) nebo prostředí PowerShell pro správu služby Azure Stack. Nejjednodušší způsob, jak informace o základních konceptech je prostřednictvím portálu. Pokud chcete pomocí prostředí PowerShell, je potřeba nainstalovat [prostředí PowerShell pro Azure Stack](asdk-post-deploy.md#install-azure-stack-powershell) a [stáhnout nástroje Azure Stack z webu GitHub](asdk-post-deploy.md#download-the-azure-stack-tools).

Azure Stack jako základní mechanismus nasazování, správu a organizace používá Azure Resource Manageru. Pokud budete ke správě služby Azure Stack a bylo možné podporovat uživatele, měli byste získat informace o Azure Resource Manageru. Další informace najdete [Začínáme s Azure Resource Manageru – dokument White Paper](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf).

## <a name="your-typical-responsibilities"></a>Typické odpovědnosti
Vaši uživatelé chtějí používat služby. Z perspektivy hlavní role je, aby tyto služby jsou pro ně dostupné. Pomocí ASDK, další služby, které nabízí, a jak se tyto služby dostupné podle [vytváření plánů, nabídek a kvót](asdk-offer-services.md). Také budete muset přidat položky na webu Marketplace, jako jsou Image virtuálních počítačů. Nejjednodušší způsob je [stažení položek z marketplace](asdk-marketplace-item.md) z Azure do služby Azure Stack.

> [!NOTE]
> Pokud chcete testovat plány, nabídky a služby, měli byste použít [portálu user portal](https://portal.local.azurestack.external); není [portálu správce](https://adminportal.local.azurestack.external).

Kromě poskytování služeb, je nutné provést všechny pravidelné úkoly operátor Azure stacku zachovat ASDK zprovozněný. Tyto povinnosti patří třeba následující:
- Přidáte uživatelské účty pro služby Azure Active Directory (Azure AD) nebo nasazení služby Active Directory Federation Services (AD FS).
- Přiřazení přístupu na základě rolí role (RBAC) ovládacího prvku (to se neomezuje jen správci)
- Monitorování stavu infrastruktury
- Správa sítě a prostředků úložiště
- Nahradit hardware počítače hostitele neúspěšné development kit 

## <a name="where-to-get-support"></a>Kde lze získat podporu
Pro development kit, je vaší jedinou možností podpory klást otázky týkající se podpory v [fóra Microsoft Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). Pokud klikněte na ikonu nápovědy a podpory (otazník) v pravém horním rohu portálu správce a potom klikněte na tlačítko **nová žádost o podporu**, přímo otevře web fór. Tato fóra jsou pravidelně monitorované. 

> [!IMPORTANT]
> Vzhledem k tomu, ASDK zkušební prostředí, neexistuje žádné oficiální podporu, kterou nabízí prostřednictvím Microsoft podporu služby zákazníkům (CSS).

## <a name="next-steps"></a>Další postup
[Nasazení ASDK](asdk-install.md)


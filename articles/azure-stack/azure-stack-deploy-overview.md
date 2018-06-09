---
title: Vyhodnocení Development Kit Azure zásobníku | Microsoft Docs
description: Zjistěte, jak nasadit Azure zásobníku Development Kit pro účely vyhodnocení.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/04/2018
ms.author: jeffgilb
ms.custom: mvc
ms.openlocfilehash: a0e742ab3ac43cc7977761dd94c9689e3a7c2e0b
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35235181"
---
# <a name="quickstart-evaluate-the-azure-stack-development-kit"></a>Rychlý úvod: vyhodnocení Azure zásobníku Development Kit

[Azure zásobníku Development Kit (ASDK)](.\asdk\asdk-what-is.md) je testovací a vývojové prostředí, které můžete nasadit k vyhodnocení a předvedení funkcí Azure zásobníku a služby. Začít s ASDK, musíte připravit hostitele hardwaru počítače a poté spusťte některé skripty (instalace trvá několik hodin). Potom můžete přihlásit k portálů správce nebo uživatele a začít používat Azure zásobníku.

## <a name="prerequisites"></a>Požadavky

### <a name="asdk-host-computer-requirements"></a>Požadavky na počítač ASDK hostitele

Před instalací ASDK, budete muset připravit počítač, který bude hostitelem development kit. Development kit hostitelský počítač, musí splňovat hardwaru, softwaru a síťové požadavky popsané v  **[zkontrolujte aspektech plánování nasazení ASDK](.\asdk\asdk-deploy-considerations.md)**.

> [!TIP]
> Můžete použít [Zkontrolujte požadavky na nasazení Azure zásobníku nástroj](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) po instalaci operačního systému na hostitelský počítač development kit potvrďte, že váš hardware splňuje všechny požadavky.

### <a name="account-requirements"></a>Požadavky na účet

Budete také muset vybrat mezi ověřováním prostřednictvím Azure Active Directory (Azure AD) nebo Active Directory Federation Services (AD FS) jako řešení identity pro vaše nasazení. Zkontrolujte požadavky na účet v  **[aspektech plánování nasazení](.\asdk\asdk-deploy-considerations.md#account-requirements)**

## <a name="download-and-extract-the-deployment-package"></a>Stažení a extrakci balíčku pro nasazení

Jakmile připravíte hostitelského počítače development kit, stažení a extrakci ASDK balíčku pro nasazení. Balíček pro nasazení zahrnuje Cloudbuilder.vhdx souboru, který je virtuálního pevného disku (VHD) s spouštěcí operačního systému, a instalační soubory protokolů Azure.

Balíček pro nasazení můžete stáhnout na development kit hostitele nebo do jiného počítače. Soubory extrahované nasazení trvat až 60 GB volného místa na disku, aby pomocí jiného počítače může pomoct snížit požadavky na úložiště na hostiteli development kit.

**[Stažení a extrakci Azure zásobníku Development Kit (ASDK)](.\asdk\asdk-download.md)**

## <a name="prepare-the-host-computer"></a>Příprava na hostitelském počítači

Před instalací ASDK, musí být připraveno prostředí hostitele a systém nastaven na spuštění z development kit virtuálního pevného disku. Po restartování hostitele, spustí se z CloudBuilder.vhdx a můžete začít nasazovat ASDK.

**[Příprava na hostitelském počítači ASDK](.\asdk\asdk-prepare-host.md)**

## <a name="install-the-asdk-on-the-host-computer"></a>Nainstalujte ASDK na hostitelském počítači

Po hostitelský počítač spustí z disku VHD, můžete nasadit do virtuálního prostředí Cloudbuilder development kit. Můžete nasadit ASDK pomocí grafického uživatelského rozhraní (GUI), poskytuje spuštěním skriptu prostředí PowerShell asdk installer.ps1 nebo z [příkazového řádku prostředí PowerShell](.\asdk\asdk-deploy-powershell.md)

> [!NOTE]
> Po hostitele spustí z bitové kopie Cloudbuilder.vhdx, máte možnost konfigurace [nastavení telemetrie zásobník Azure](.\asdk\asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *před* instalaci ASDK.

**[Nainstalovat Azure zásobníku Development Kit (ASDK)](.\asdk\asdk-install.md)**

## <a name="perform-post-deployment-configurations"></a>Provede konfigurace po nasazení

Po instalaci ASDK, existuje několik doporučených kontroly po instalaci a změny konfigurace, které by měly být.

**Nástroje**

Instalace nástroje Azure PowerShell zásobníku a Githubu a zkontrolujte úspěšné instalace pomocí rutiny test-AzureStack.

**Řešení identit**

Pro nasazení, které používá Azure AD je nutné aktivovat obou zásobník Azure správce a klienta portálů. Tato aktivace souhlasí s uvedením zásobník Azure portal a Azure Resource Manager správná oprávnění (uvedené na stránce souhlas) pro všechny uživatele adresáři.

**Vypršení platnosti hesla**

Má resetovat zásad vypršení platnosti hesla a ujistěte se, že se heslo pro hostitele development kit nevyprší před ukončením svého zkušební období.

> [!NOTE]
> Máte také možnost konfigurace [nastavení telemetrie zásobník Azure](.\asdk\asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *po* instalaci ASDK.

**[Úlohy nasazení POST-ASDK](.\asdk\asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Zaregistrovat Azure

Je nutné, aby bylo možné zaregistrovat zásobník Azure s Azure [stažení položky Azure marketplace](.\asdk\asdk-marketplace-item.md) do protokolů Azure.

**[Zaregistrovat Azure zásobník Azure](.\asdk\asdk-register.md)**

## <a name="next-steps"></a>Další postup

Blahopřejeme! Pomocí kroků v této quickstart máte ASDK prostředí s [správce](https://adminportal.local.azurestack.external) portál a [uživatele](https://portal.local.azurestack.external) portálu.

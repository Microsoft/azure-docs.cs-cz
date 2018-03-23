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
ms.date: 03/22/2018
ms.author: jeffgilb
ms.custom: mvc
ms.openlocfilehash: 1deabcf64b3fbf3cbc89232c340a8882cd2591e8
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="quickstart-evaluate-the-azure-stack-development-kit"></a>Rychlý úvod: Vyhodnocení Development Kit Azure zásobníku
[Azure zásobníku Development Kit (ASDK)](.\asdk\asdk-what-is.md) je testovací a vývojové prostředí, které můžete nasadit k vyhodnocení a předvedení funkcí Azure zásobníku a služby. Začít s ASDK, musíte připravit hostitele hardwaru počítače a poté spusťte některé skripty (instalace trvá několik hodin). Potom můžete přihlásit k portálů správců a uživatelů a začít používat Azure zásobníku.

## <a name="prerequisites"></a>Požadavky 
Před instalací ASDK, budete muset připravit počítač, který bude hostitelem development kit (hostitel development kit). Hostitelský počítač development kit musí splňovat minimální požadavky na hardware, software a síť. 

Budete také muset vybrat mezi ověřováním prostřednictvím Azure Active Directory (Azure AD) nebo Active Directory Federation Services (AD FS) jako řešení identity pro vaše nasazení. 

Ujistěte se, že hostitel development kit splňuje minimální požadavky na hardware a jste provedli vaše rozhodnutí řešení identity provedené před spuštěním nasazení tak, aby proces instalace bez problémů. 

**[Přečtěte si informace o plánování nasazení ASDK](.\asdk\asdk-deploy-considerations.md)**

> [!TIP]
> Můžete použít [Zkontrolujte požadavky na nasazení Azure zásobníku nástroj](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) po instalaci operačního systému na hostitelský počítač development kit potvrďte, že váš hardware splňuje všechny požadavky.

## <a name="download-and-extract-the-deployment-package"></a>Stažení a extrakci balíčku pro nasazení
Jakmile ověříte, že hostitelského počítače development kit splňuje základní požadavky pro instalaci ASDK, je dalším krokem pro stažení a extrakci ASDK balíčku pro nasazení. Balíček pro nasazení obsahuje Cloudbuilder.vhdx soubor, který je virtuální pevný disk, který obsahuje spouštěcí operační systém a instalační soubory protokolů Azure.

Balíček pro nasazení můžete stáhnout na development kit hostitele nebo do jiného počítače. Soubory extrahované nasazení trvat až 60 GB volného místa na disku, aby pomocí jiného počítače může pomoct snížit požadavky na hardware pro hostitele development kit.

**[Stažení a extrakci Azure zásobníku Development Kit (ASDK)](.\asdk\asdk-download.md)**

## <a name="prepare-the-development-kit-host-computer"></a>Příprava hostitelský počítač development kit
Před instalací ASDK na hostitelském počítači, musí být připraveno prostředí a systém konfigurován pro spouštění z virtuálního pevného disku. Po development kit hostitelský počítač připravený, spustí z pevného disku virtuálního počítače CloudBuilder.vhdx tak, abyste ji mohli začít ASDK nasazení.

**[Příprava na hostitelském počítači ASDK](.\asdk\asdk-prepare-host.md)**

## <a name="install-the-asdk-on-the-host-computer"></a>Nainstalujte ASDK na hostitelském počítači
Jakmile připravíte development kit hostitelský počítač, se dá nasadit ASDK do CloudBuilder.vhdx bitové kopie. ASDK se dá nasadit pomocí grafického uživatelského rozhraní (GUI) zadané při stažení a spuštění skriptu PowerShell asdk installer.ps1 nebo zcela [příkazového řádku](.\asdk\asdk-deploy-powershell.md). 

> [!NOTE]
> Volitelně můžete po do CloudBuilder.vhdx hostitelský počítač, můžete nakonfigurovat [nastavení telemetrie zásobník Azure](.\asdk\asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *před* instalaci ASDK.


**[Nainstalovat Azure zásobníku Development Kit (ASDK)](.\asdk\asdk-install.md)**

## <a name="perform-post-deployment-configurations"></a>Provede konfigurace po nasazení
Po instalaci ASDK, existuje několik doporučených kontroly po instalaci a změny konfigurace, které by měly být. Můžete ověřit instalaci byla nainstalována úspěšně pomocí rutiny test-AzureStack a nainstalujte nástroje pro Azure PowerShell zásobníku a Githubu. 

Po nasazení, které používají Azure AD je nutné aktivovat obou zásobník Azure správce a klienta portálů. Tato aktivace souhlasí s uvedením zásobník Azure portal a Azure Resource Manager správná oprávnění (uvedené na stránce souhlas) pro všechny uživatele adresáři.

Také byste měli obnovit zásad vypršení platnosti hesla a ujistěte se, že se heslo pro hostitele development kit nevyprší před ukončením svého zkušební období.

> [!NOTE]
> Volitelně můžete také nakonfigurovat [nastavení telemetrie zásobník Azure](.\asdk\asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *po* instalaci ASDK.

**[Úlohy nasazení POST-ASDK](.\asdk\asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Zaregistrovat Azure
Je nutné, aby bylo možné zaregistrovat zásobník Azure s Azure [stažení položky Azure marketplace](.\asdk\asdk-marketplace-item.md) do protokolů Azure.

**[Zaregistrovat Azure zásobník Azure](.\asdk\asdk-register.md)**

## <a name="next-steps"></a>Další postup
Blahopřejeme! Po dokončení těchto kroků, budete mít vývojovém prostředí sady s oběma [správce](https://adminportal.local.azurestack.external) a [uživatele](https://portal.local.azurestack.external) portálů. 

---
title: Vyzkoušejte Azure Stack Development Kit | Dokumentace Microsoftu
description: Zjistěte, jak nasadit Azure Stack Development Kit pro účely vyhodnocení.
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
ms.date: 10/15/2018
ms.author: jeffgilb
ms.custom: mvc
ms.openlocfilehash: bf07fe56c65e53f5485b9927e0d704f80842cf3a
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49338692"
---
# <a name="quickstart-evaluate-the-azure-stack-development-kit"></a>Rychlý start: vyhodnocení Azure Stack Development Kit

[Azure Stack Development Kit (ASDK)](.\asdk\asdk-what-is.md) je vývoj a testování prostředí, které můžete nasadit k vyhodnocení a k předvedení funkcí služby Azure Stack a služeb. Abyste mohli začít s ASDK, budete muset Příprava hostitelském hardwaru počítače a pak spusťte některé skripty (instalace trvá několik hodin). Potom můžete přihlásit na portály správci nebo uživateli chcete začít používat Azure Stack.

## <a name="prerequisites"></a>Požadavky

### <a name="asdk-host-computer-requirements"></a>Požadavky na počítač ASDK hostitele

Před instalací ASDK, budete muset připravit počítač, který bude hostitelem development kit. Development kit hostitelský počítač musí splňovat hardwaru, softwaru, a podle požadavků na síť  **[zkontrolujte aspektech plánování nasazení ASDK](.\asdk\asdk-deploy-considerations.md)**.

> [!TIP]
> Můžete použít [Zkontrolujte požadavky na nasazení služby Azure Stack nástroj](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) po instalaci operačního systému na vývojovém počítači hostitele kit potvrďte, že váš hardware splňuje všechny požadavky.

### <a name="account-requirements"></a>Požadavky na účet

Také musíte zvolit mezi použitím nástrojů Azure Active Directory (Azure AD) nebo Active Directory Federation Services (AD FS) jako řešení identit pro vaše nasazení. Zkontrolujte požadavky na účet v  **[aspektech plánování nasazení](.\asdk\asdk-deploy-considerations.md#account-requirements)**

## <a name="download-and-extract-the-deployment-package"></a>Stáhněte a rozbalte balíček pro nasazení

Jakmile připravíte hostitelského počítače development kit, stažení a extrakci ASDK balíček pro nasazení. Balíček pro nasazení obsahuje Cloudbuilder.vhdx soubor, který je virtuálního pevného disku (VHD) s operačním systémem spouštěcí a instalační soubory služby Azure Stack.

Balíček pro nasazení můžete stáhnout na hostitele development kit, nebo do jiného počítače. Soubory extrahované nasazení trvat až 60 GB volného místa na disku, takže na jiném počítači může pomoct snížit požadavky na úložiště na hostiteli development kit.

**[Stažení a extrakci Azure Stack Development Kit (ASDK)](.\asdk\asdk-download.md)**

## <a name="prepare-the-host-computer"></a>Příprava hostitelském počítači

Před instalací ASDK hostitelského prostředí musí být připravené a systém nakonfigurován na spuštění ze development kit virtuálního pevného disku. Po restartování hostitele se spouští z CloudBuilder.vhdx a může začít nasazovat ASDK.

**[Příprava hostitelském počítači ASDK](.\asdk\asdk-prepare-host.md)**

## <a name="install-the-asdk-on-the-host-computer"></a>Nainstalujte ASDK v hostitelském počítači

Po spuštění počítače hostitele z virtuálního pevného disku, můžete nasadit vývojová sada Cloudbuilder virtuální prostředí. Můžete nasadit ASDK pomocí grafického uživatelského rozhraní (GUI), spuštěním skriptu prostředí PowerShell asdk installer.ps1 osobně, nebo z [příkazového řádku Powershellu](.\asdk\asdk-deploy-powershell.md)

> [!NOTE]
> Po spuštění hostitele z Cloudbuilder.vhdx image, máte možnost konfigurace [nastavení telemetrie Azure Stack](.\asdk\asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *před* instalaci ASDK.

**[Instalace sady Azure Stack Development Kit (ASDK)](.\asdk\asdk-install.md)**

## <a name="perform-post-deployment-configurations"></a>Provedení konfigurace po nasazení

Po instalaci ASDK, existuje několik doporučených kontroly po instalaci a změny konfigurace, které by měly být.

**Nástroje**

Instalace nástrojů pro Azure Stack Powershellu a GitHub a zkontrolovat úspěšné instalace pomocí rutiny test-AzureStack.

**Řešení identit**

Pro nasazení, které používá služby Azure AD je nutné aktivovat i Azure Stack správce a tenanta portály. Tato aktivace vyjádří souhlas poskytuje správná oprávnění (uvedené na stránce souhlas) pro všechny uživatele adresáře portálu Azure Stack a Azure Resource Manageru.

**Vypršení platnosti hesla**

Měli byste resetovat zásady vypršení platnosti hesla, abyste měli jistotu, že heslo pro hostitele development kit platnost pasu nevyprší před vám zkušební období skončí.

> [!NOTE]
> Máte také možnost konfigurace [nastavení telemetrie Azure Stack](.\asdk\asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *po* instalace ASDK.

**[Úlohy nasazení ASDK příspěvku](.\asdk\asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Zaregistrujte v Azure

Azure Stack musí registraci v Azure, abyste mohli [stažení položek z Azure marketplace](.\asdk\asdk-marketplace-item.md) do služby Azure Stack.

**[Registrace Azure Stack s Azure](.\asdk\asdk-register.md)**

## <a name="next-steps"></a>Další postup

Blahopřejeme! Pomocí kroků v tomto rychlém startu jste ASDK prostředí s [správce](https://adminportal.local.azurestack.external) portál a [uživatele](https://portal.local.azurestack.external) portálu.

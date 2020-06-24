---
title: Přehled zřizování Linux
description: Přehled toho, jak přenést image virtuálních počítačů se systémem Linux nebo vytvořit nové image pro použití v Azure.
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: overview
ms.workload: infrastructure
ms.date: 06/22/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 6530d05b8e1aa565e64256054e81b785572edfb0
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2020
ms.locfileid: "85308139"
---
# <a name="azure-linux-vm-provisioning"></a>Zřizování virtuálních počítačů Azure Linux
Když vytváříte virtuální počítač z generalizované Image (sdílená Galerie imagí nebo spravovaná image), řídicí plocha vám umožní vytvořit virtuální počítač a předat parametry a nastavení virtuálnímu počítači. Toto se říká *zřizování*virtuálního počítače. Během zřizování platforma zpřístupňuje virtuálnímu počítači požadované hodnoty parametrů pro vytvoření virtuálního počítače (název hostitele, uživatelské jméno, heslo, klíče SSH, customData), které jsou k dispozici pro virtuální počítač. 

Zřizovací agent vloženými v rámci image bude rozhraní s platformou, propojuje až několik nezávislých rozhraní zřizování, nastaví vlastnosti a signál na platformu, kterou dokončil. 

Agenti zřizování můžou být [agentem Azure Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)nebo [Cloud-init](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init). Jedná se o [předpoklady](create-upload-generic.md) pro vytváření zobecněných imagí.

Zřizovací agenti poskytují podporu pro všechny schválené [distribuce systému Azure Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)a v mnoha případech najdete schválené image distribuce, které se budou dodávat s agenty Cloud-init i Linux. Díky tomu máte možnost mít ke zpracování zřizování Cloud-init a Agent pro Linux bude poskytovat podporu pro zpracování [rozšíření Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/features-windows). Poskytnutí podpory pro rozšíření znamená, že virtuální počítač bude mít nárok na podporu dalších služeb Azure, jako je resetování hesla virtuálních počítačů, monitorování Azure, Azure Backup, šifrování disku Azure atd.

Po dokončení zřizování se při každém spuštění spustí Cloud-init. Cloud-init bude monitorovat změny virtuálního počítače, například změny sítě, připojení a formátování dočasného disku a spuštění agenta pro Linux. Agent pro Linux se průběžně spouští na serveru a hledá "cílový stav" (novou konfiguraci) z platformy Azure, takže když nainstalujete rozšíření, Agent bude moct je zpracovat.

I když jsou aktuálně dva agenti zřizování, Cloud-init by měl být agentem zřizování, který zvolíte, a Agent pro Linux by měl být nainstalován pro podporu rozšíření. Díky tomu můžete využívat optimalizace platforem a máte možnost zakázat nebo odebrat agenta pro Linux, kde najdete další podrobnosti o tom, jak vytvářet image bez agenta, a jak ho odebrat, přečtěte si prosím tuto [dokumentaci](disable-provisioning.md).

Pokud máte jádro systému Linux, které nemůže podporovat spuštění agenta, ale chcete nastavit některé vlastnosti vytváření virtuálních počítačů, jako je název hostitele, customData, uživatelské jméno, heslo a klíče SSH, pak v tomto dokumentu popisuje, jak můžete [vytvářet generalizované image bez agenta](no-agent.md)a splňovat požadavky na platformu.


## <a name="provisioning-agent-responsibilities"></a>Odpovědnosti agenta zřizování

**Zřizování imagí**
  
- Vytvoření uživatelského účtu
- Konfigurace typů ověřování SSH
- Nasazení veřejných klíčů SSH a párů klíčů
- Nastavení názvu hostitele
- Publikování názvu hostitele na platformě DNS platformy
- Vytváření sestav otisků klíčů hostitele SSH pro platformu
- Správa disků prostředků
- Formátování a připojení disku prostředků
- Využívání a zpracování`customData`
 
**Sítě**
  
- Spravuje trasy pro zlepšení kompatibility s platformami serverů DHCP.
- Zajišťuje stabilitu názvu síťového rozhraní.

**jádro**
  
- Nakonfiguruje virtuální technologii NUMA (zakázat pro jádro <`2.6.37` ).
- Spotřebovává entropii technologie Hyper-V pro`/dev/random`
- Konfiguruje časové limity SCSI pro kořenové zařízení (které by mohlo být vzdálené).

**Diagnostika**
  
- Přesměrování konzoly na sériový port

## <a name="communication"></a>Communication
Tok informací z platformy k agentům probíhá prostřednictvím dvou kanálů:

- Spouštěcí DVD připojený k IaaS nasazení. Disk DVD obsahuje konfigurační soubor kompatibilní s OVF, který obsahuje informace o všech zřizováních, jiné než skutečné páry klíčů SSH.
- Koncový bod TCP vystavuje REST API, který se používá k získání nasazení a konfiguraci topologie.


## <a name="azure-provisioning-agent-requirements"></a>Požadavky na agenta zřizování Azure
Agent pro Linux a Cloud-init závisí na některých systémových balíčcích, aby fungovaly správně:
- Python 2.6 +
- OpenSSL 1.0 +
- OpenSSH 5.3 +
- Nástroje systému souborů: `sfdisk` , `fdisk` , `mkfs` ,`parted`
- Nástroje pro heslo: chpasswd, sudo
- Nástroje pro zpracování textu: sed, grep
- Síťové nástroje: IP-Route
- Podpora jádra pro připojení systémů souborů UDF.

## <a name="next-steps"></a>Další kroky

Pokud potřebujete, můžete [Zakázat zřizování a odebrat agenta pro Linux](disable-provisioning.md).

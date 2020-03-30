---
title: Konfigurace nastavení proxy služby Mobility service pro Azure to Azure Disaster Recovery | Dokumenty společnosti Microsoft
description: Obsahuje podrobnosti o konfiguraci služby mobility, když zákazníci používají proxy server ve svém zdrojovém prostředí.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2020
ms.author: sideeksh
ms.openlocfilehash: 3d33b5a89a718a41e5c547551f6e7eb4f7033a63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503124"
---
# <a name="configure-mobility-service-proxy-settings-for-azure-to-azure-disaster-recovery"></a>Konfigurace nastavení proxy služby Mobility service pro Azure to Azure Disaster Recovery

Tento článek obsahuje pokyny k přizpůsobení konfigurací sítí na cílovém virtuálním počítači Azure (VM) při replikaci a obnovení virtuálních počítačů Azure z jedné oblasti do druhé pomocí [Azure Site Recovery](site-recovery-overview.md).

Účelem tohoto dokumentu je poskytnout kroky ke konfiguraci nastavení proxy služby Azure Site Recovery Mobility Service ve scénáři Azure to Azure Disaster Recovery. 

Servery proxy jsou síťové brány, které umožňují nebo zamítají síťová připojení koncovým bodům. Proxy server je obvykle počítač mimo klientský počítač, který se pokouší o přístup k koncovým bodům sítě. Seznam obtok umožňuje klientovi provádět připojení přímo ke koncovým bodům bez procházení proxy serveru. Uživatelské jméno a heslo může být volitelně nastaveno pro proxy server správci sítě, takže pouze ověření klienti mohou používat proxy server. 

## <a name="before-you-start"></a>Než začnete

Zjistěte, jak site recovery poskytuje zotavení po havárii pro [tento scénář](azure-to-azure-architecture.md).
Seznamte se [s pokyny k síti,](azure-to-azure-about-networking.md) když replikujete a obnovujete virtuální počítače Azure z jedné oblasti do druhé pomocí [Azure Site Recovery](site-recovery-overview.md).
Ujistěte se, že je proxy server nastaven správně na základě potřeb vaší organizace.

## <a name="configure-the-mobility-service"></a>Konfigurace služby mobility

Služba mobility podporuje pouze neověřené proxy servery. Poskytuje dva způsoby, jak zadat podrobnosti serveru proxy pro komunikaci s koncovými body obnovení webu. 

### <a name="method-1-auto-detection"></a>Metoda 1: Automatická detekce

Služba Mobility Service automaticky rozpozná nastavení proxy serveru z nastavení prostředí nebo nastavení aplikace IE (pouze systém Windows) během povolení replikace. 

- Operační systém Windows: Během povolení replikace služba Mobility zjišťuje nastavení serveru proxy nakonfigurované v aplikaci Internet Explorer pro uživatele místního systému. Chcete-li nastavit proxy server pro účet místního systému, může správce pomocí psexec spustit příkazový řádek a potom aplikaci Internet Explorer. 
- Operační systém Windows: Nastavení proxy serveru je konfigurováno jako proměnné prostředí http_proxy a no_proxy. 
- Operační systém Linux: Nastavení proxy serveru je konfigurováno v /etc/profile nebo /etc/environment jako proměnné prostředí http_proxy, no_proxy. 
- Automaticky zjištěné nastavení proxy serveru je uloženo do konfiguračního souboru proxy serveru Proxy Služby mobility ProxyInfo.conf 
- Výchozí umístění serveru ProxyInfo.conf 
    - Windows: C:\ProgramData\Microsoft Azure Site Recovery\Config\ProxyInfo.conf 
    - Linux: /usr/local/InMage/config/ProxyInfo.conf


### <a name="method-2-provide-custom-application-proxy-settings"></a>Metoda 2: Poskytnutí vlastního nastavení proxy serveru aplikace

V tomto případě zákazník poskytuje vlastní nastavení proxy aplikace v konfiguračním souboru služby Mobility Service ProxyInfo.conf. Tato metoda umožňuje zákazníkům poskytovat proxy server pouze pro službu Mobility Service nebo jiný proxy server pro službu Azure Site Recovery Mobility Service než proxy server (nebo žádný proxy server) pro ostatní aplikace v počítači.

## <a name="proxy-template"></a>Šablona proxy
ProxyInfo.conf obsahuje následující šablonu [proxy] Adresa=http://1.2.3.4 Port=5678 BypassList=hypervrecoverymanager.windowsazure.com,login.microsoftonline.com,blob.core.windows.net. BypassList nepodporuje zástupné znaky jako '*.windows.net', ale dává windows.net je dost dobrý, aby obejít. 

## <a name="next-steps"></a>Další kroky:
- Přečtěte si [pokyny k síti](site-recovery-azure-to-azure-networking-guidance.md) pro replikaci virtuálních počítačů Azure.
- Nasazení zotavení po havárii [replikací virtuálních počítačích Azure](site-recovery-azure-to-azure.md).
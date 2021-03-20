---
title: Konfigurace nastavení proxy služby mobility pro Azure do Azure – zotavení po havárii | Microsoft Docs
description: Poskytuje podrobné informace o tom, jak nakonfigurovat službu mobility, když zákazníci používají proxy server ve svém zdrojovém prostředí.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2020
ms.author: sideeksh
ms.openlocfilehash: 429ffcab147142ae2e96de13b7c9e1e5ee1ac7ba
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86133232"
---
# <a name="configure-mobility-service-proxy-settings-for-azure-to-azure-disaster-recovery"></a>Konfigurace nastavení proxy služby mobility pro Azure do Azure – zotavení po havárii

Tento článek poskytuje pokyny k přizpůsobení síťových konfigurací v cílovém virtuálním počítači Azure při replikaci a obnovování virtuálních počítačů Azure z jedné oblasti do druhé pomocí [Azure Site Recovery](site-recovery-overview.md).

Účelem tohoto dokumentu je poskytnout kroky ke konfiguraci nastavení proxy serveru pro službu Azure Site Recovery mobility ve scénáři zotavení po havárii Azure do Azure. 

Proxy jsou síťové brány, které povolují nebo zakazují síťová připojení k koncovým bodům. Proxy server je typicky počítač mimo klientský počítač, který se pokouší získat přístup k koncovým bodům sítě. Seznam obcházení umožňuje klientovi, aby se připojili přímo k koncovým bodům bez procházení proxy serverem. Uživatelské jméno a heslo mohou být volitelně nastaveny pro správce sítě, aby mohli proxy používat jenom ověření klienti. 

## <a name="before-you-start"></a>Než začnete

Přečtěte si, jak Site Recovery poskytuje zotavení po havárii pro [Tento scénář](azure-to-azure-architecture.md).
Seznamte se s [pokyny k síti](azure-to-azure-about-networking.md) při replikaci a obnovování virtuálních počítačů Azure z jedné oblasti do druhé pomocí [Azure Site Recovery](site-recovery-overview.md).
Ujistěte se, že je váš proxy správně nastavený podle potřeb vaší organizace.

## <a name="configure-the-mobility-service"></a>Konfigurace služby mobility

Služba mobility podporuje jenom neověřené proxy servery. Poskytuje dva způsoby, jak zadat podrobnosti o proxy serveru pro komunikaci s koncovými body Site Recovery. 

### <a name="method-1-auto-detection"></a>Metoda 1: automatické zjišťování

Služba mobility automaticky detekuje nastavení proxy serveru z nastavení prostředí nebo nastavení IE (pouze Windows) během povolování replikace. 

- OPERAČNÍ systém Windows: během povolování replikace služba mobility detekuje nastavení proxy serveru, jak je nakonfigurované v Internet Exploreru pro uživatele místního systému. K nastavení proxy serveru pro místní systémový účet může správce použít PsExec ke spuštění příkazového řádku a potom k aplikaci Internet Explorer. 
- OPERAČNÍ systém Windows: nastavení proxy serveru jsou nakonfigurovaná jako proměnné prostředí http_proxy a no_proxy. 
- Linux OS: nastavení proxy serveru jsou nakonfigurovaná v/etc/Profile nebo/etc/Environment jako proměnné prostředí http_proxy, no_proxy. 
- Automaticky zjištěná nastavení proxy se ukládají do souboru konfigurace proxy služby mobility ProxyInfo. conf. 
- Výchozí umístění ProxyInfo. conf 
    - Windows: C:\ProgramData\Microsoft Azure Site Recovery\Config\ProxyInfo.conf 
    - Linux:/usr/local/InMage/config/ProxyInfo.conf


### <a name="method-2-provide-custom-application-proxy-settings"></a>Metoda 2: zadání nastavení proxy vlastní aplikace

V takovém případě zákazník poskytne nastavení proxy vlastní aplikace v konfiguračním souboru služby mobility ProxyInfo. conf. Tato metoda umožňuje zákazníkům poskytnout proxy jenom pro službu mobility nebo jiný proxy server pro Azure Site Recovery služby mobility, než je proxy server (nebo žádný proxy server) pro zbývající aplikace v počítači.

## <a name="proxy-template"></a>Šablona proxy
ProxyInfo. conf obsahuje následující šablonu [proxy] address = http://1.2.3.4 port = 5678 BypassList = hypervrecoverymanager. windowsazure. com, Login. microsoftonline. com, BLOB. Core. Windows. NET. BypassList nepodporuje zástupné znaky, jako je *. windows.net, ale poskytnutí windows.net je dostatečně dobré pro obejití. 

## <a name="next-steps"></a>Další kroky:
- Přečtěte si [pokyny k síti](./azure-to-azure-about-networking.md)  pro replikaci virtuálních počítačů Azure.
- Nasazení zotavení po havárii [replikací virtuálních počítačů Azure](./azure-to-azure-quickstart.md).

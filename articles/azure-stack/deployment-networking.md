---
title: Azure zásobníku nasazení síťový provoz | Microsoft Docs
description: Tento článek popisuje, co mohou očekávat, o procesech sítě Azure zásobníku nasazení.
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
ms.date: 05/21/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: b808875e66e867b84e2971c6a5bd031d108d003b
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654807"
---
# <a name="about-deployment-network-traffic"></a>O síťovém provozu nasazení
Principy tok síťových přenosů během zásobník Azure nasazení je důležité k zajištění úspěšné nasazení. Tento článek vás provede procesem očekávané síťového provozu během procesu nasazení zajistit představu o co mají očekávat.

Tento obrázek znázorňuje všechny komponenty a připojení zahrnutých v procesu nasazení:

![Azure zásobníku nasazení síťové topologie](media/deployment-networking/figure1.png)

> [!NOTE]
> Tento článek popisuje požadavky pro nasazení, připojené, další informace o dalších metodách nasazení naleznete v tématu [modelů připojení nasazení Azure zásobníku](azure-stack-connection-models.md).

### <a name="the-deployment-vm"></a>Nasazení virtuálních počítačů
Řešení Azure zásobníku obsahuje skupinu serverů, které budou použity k hostování součásti zásobníku Azure a navíc serveru s názvem hostitele životního cyklu hardwaru (HLH). Tento server se používá k nasazení a správa životního cyklu řešení a hostitelem nasazení virtuálních počítačů (DVM) během nasazení.

## <a name="deployment-requirements"></a>Požadavky na nasazení
Před zahájením nasazení, nejsou některé minimální požadavky, které může být ověřen výrobce OEM zajistit, že nasazení úspěšně dokončena. Porozumět tomu, že tyto požadavky vám pomůže připravit prostředí a ujistěte se, že ověření úspěšné, jsou následující:

-   [Certifikáty](azure-stack-pki-certs.md)
-   [Předplatné Azure](https://azure.microsoft.com/free/?b=17.06)
-   Přístup k internetu
-   DNS
-   NTP

> [!NOTE]
> Tento článek se zaměřuje na poslední tři požadavky. Další informace o první dvě najdete v části výše uvedené odkazy.

## <a name="deployment-network-traffic"></a>Nasazení síťových přenosů
DVM je nakonfigurován pomocí IP adresy z BMC sítě a vyžaduje síťový přístup k Internetu. I když ne všechny součásti BMC sítě vyžadují externí směrování nebo přístup k Internetu, některé součásti výrobce OEM využití IP adres z této sítě může také vyžadovat ho.

Během nasazení ověřuje DVM proti Azure Active Directory (Azure AD) pomocí účtu Azure ze svého předplatného. Chcete-li tak učinit, DVM vyžaduje přístup k Internetu na seznam konkrétních portů a adres URL. Můžete najít úplný seznam v [publikování koncové body](azure-stack-integrate-endpoints.md) dokumentaci. DVM bude využívat serveru DNS pro předávání DNS požadavkům interní součásti na externí adresy URL. Interní DNS předává tyto požadavky na adresu služby pro předávání DNS, které poskytujete výrobci OEM před jejich nasazením. Totéž platí pro NTP server, spolehlivé čas serveru je potřeba zachování konzistence a čas synchronizace pro všechny součásti zásobníku Azure.

Přístup k Internetu vyžaduje DVM během nasazení je pouze odchozí, žádná příchozí volání probíhají během nasazení. Mějte na paměti, že jeho IP se používá jako zdroj a tohoto balíku Azure nepodporuje konfigurace proxy serveru. Proto v případě potřeby, potřebujete poskytovat transparentní server proxy nebo NAT pro přístup k Internetu. Po dokončení nasazení veškerá komunikace mezi Azure a Azure zásobníku jsou vytvářeny v externí síti pomocí veřejná virtuální IP adresy.

Konfigurace sítě na Azure zásobníku přepínače obsahují seznamy řízení přístupu (ACL), omezit přenos dat mezi určitých sítě zdroje a cíle. DVM se pouze komponenty s neomezeným přístupem; i HLH je velmi omezený. Zeptejte se na výrobce OEM možnosti přizpůsobení k usnadnění správy a přístupu z vaší sítě. Z důvodu tyto seznamy ACL je důležité k zamezení Změna adresy serverů DNS a NTP v době nasazení. Pokud tak učiníte, musíte znovu nakonfigurovat všechny přepínačů pro řešení.

Po dokončení nasazení zadané adresy serveru DNS a NTP bude nadále používat součásti systému přímo. Například pokud jste po dokončení nasazení zkontrolovat požadavky na DNS, zdroj se změní z IP adresy DVM na adresu z rozsahu externí síť.

Po úspěšném nasazení Azure zásobníku, může váš partner OEM používat DVM pro další úlohy po nasazení. Ale po dokončení všech úloh nasazení a konfigurace po nasazení OEM musí odebrat a odstranění DVM z HLH.

## <a name="next-steps"></a>Další postup
[Ověření registrace Azure](azure-stack-validate-registration.md)

---
title: Azure Stack nasazení síťový provoz | Dokumentace Microsoftu
description: Tento článek popisuje, co můžete očekávat o sítě procesech nasazování služby Azure Stack.
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
ms.date: 08/30/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: aac8713f94482e0fc809f24786b96d29b23b076a
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43343384"
---
# <a name="about-deployment-network-traffic"></a>Informace o nasazení síťového provozu
Vysvětlení, jak se provoz probíhá během Azure Stack nasazení je důležité k zajištění úspěšné nasazení. Tento článek vás provede očekávané síťového provozu během procesu nasazení poskytnout představu, co mají očekávat.

Tento obrázek znázorňuje všechny komponenty a připojení zahrnutých v procesu nasazení:

![Topologie sítě nasazení služby Azure Stack](media/deployment-networking/figure1.png)

> [!NOTE]
> Tento článek popisuje požadavky pro připojené nasazení, informace o dalších metodách nasazení naleznete v tématu [modelů připojení nasazení Azure Stack](azure-stack-connection-models.md).

### <a name="the-deployment-vm"></a>Nasazení virtuálního počítače
Řešení Azure Stack obsahuje skupinu serverů, které se používají k hostování součásti služby Azure Stack a navíc serveru s názvem hostitele životního cyklu hardwaru (HLH). Tento server se používá k nasazení a správa životního cyklu vašeho řešení a hostitelem nasazení virtuálního počítače (DVM) během nasazení.

## <a name="deployment-requirements"></a>Požadavky na nasazení
Před zahájením nasazení, existují některé minimální požadavky, které můžete ověřit pomocí vašeho OEM dodavatele zajistit, že se nasazení dokončí úspěšně. Pochopení že těchto požadavků vám pomůže připravit prostředí a ujistěte se, že ověření proběhne úspěšně, jsou:

-   [Certifikáty](azure-stack-pki-certs.md)
-   [Předplatné Azure](https://azure.microsoft.com/free/?b=17.06)
-   Přístup k internetu
-   DNS
-   NTP

> [!NOTE]
> Tento článek se zaměřuje na poslední tři požadavky. Další informace o první dva najdete v článku odkazy uvedené výše.

## <a name="deployment-network-traffic"></a>Nasazení síťových přenosů
DVM má nakonfigurovanou IP adresy z BMC sítě a vyžaduje přístup k síti k Internetu. I když nejsou všechny součásti BMC sítě vyžadují externí směrování nebo přístup k Internetu, některé součásti specifické pro výrobce OEM využívání IP adres z této sítě může také vyžadovat ho.

Během nasazení DVM ověřuje vůči Azure Active Directory (Azure AD) pomocí účtu Azure ze svého předplatného. Aby bylo možné Uděláte to tak, DVM vyžaduje přístup k Internetu na seznam konkrétních portů a adres URL. Najdete kompletní seznam v [publikování koncových bodů](azure-stack-integrate-endpoints.md) dokumentaci. DVM budou využívat serveru DNS pro předávání DNS požadavky vnitřní součásti na externí adresy URL. Interní server DNS předává tyto požadavky na adresu předávání DNS, které poskytujete výrobci OEM před jejich nasazením. Totéž platí pro NTP server, spolehlivé čas serveru se vyžaduje kvůli zachování konzistence a čas synchronizace pro všechny součásti služby Azure Stack.

Přístup k Internetu vyžaduje DVM během nasazení je pouze odchozí, žádná příchozí volání jsou prováděny během nasazení. Mějte na paměti, že jako zdroj používá jeho IP a že služby Azure Stack nepodporuje konfigurace proxy serveru. Proto v případě potřeby budete muset zadat transparentní proxy server nebo NAT pro přístup k Internetu. Během nasazení některé vnitřní součásti začne, přístup k Internetu prostřednictvím externí síť používání veřejných virtuálních IP adres. Po dokončení nasazení veškerou komunikaci mezi Azure a Azure Stack se provádí prostřednictvím externí sítě pomocí veřejných virtuálních IP adres.

Konfigurace sítě v Azure stacku přepínače obsahovat seznamů řízení přístupu (ACL), který omezení přenosu dat mezi určité síti zdroje a cíle. DVM se pouze komponenty s neomezeným přístupem; dokonce i HLH je velmi omezený. Zeptejte se na vašeho OEM dodavatele možnosti vlastního nastavení pro usnadnění správy a přístup z vaší sítě. Z důvodu tyto seznamy ACL je důležité vyhnout změně adresy serverů DNS a NTP v době nasazení. Pokud tak učiníte, musíte znovu nakonfigurovat všechny přepínače pro řešení.

Po dokončení nasazení bude nadále využívat systémové součásti přímo zadané adresy serveru DNS a NTP. Například pokud zaškrtnete žádosti DNS po dokončení nasazení, zdroj změní z DVM IP adresa z rozsahu externí síť.

Po dokončení nasazení zadané adresy serveru DNS a NTP bude nadále používat součásti prostřednictvím SDN pomocí externí síti. Například pokud zaškrtnete žádosti DNS po dokončení nasazení, zdroj se změní z IP adresy DVM na veřejných virtuálních IP adres.

## <a name="next-steps"></a>Další postup
[Ověření registrace služby Azure](azure-stack-validate-registration.md)

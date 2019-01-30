---
title: Auditování Azure Stack fyzického zařízení
description: Zjistěte, jak integrovat auditování přístupu k fyzického zařízení ve službě Azure Stack
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 11/05/2018
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 11/05/2018
keywords: ''
ms.openlocfilehash: 8622619c56b618a1f5e4c91efcd047ab0ba9ce0a
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247693"
---
# <a name="azure-stack-datacenter-integration---physical-device-auditing"></a>Integrace datových center Azure Stack – auditování fyzického zařízení

Všechny fyzické zařízení ve službě Azure Stack, jako jsou řadiče pro správu základní desky (BMC) a síťové přepínače, vysílala protokoly auditu. Protokoly auditu můžete integrovat vaše celkové řešení auditování. Vzhledem k tomu, že zařízení se liší v různých dodavatelů hardware Azure Stack pro výrobce OEM, obraťte se na dodavatele dokumentaci k integraci auditování.
Následující části obsahují některé obecné informace pro auditování fyzického zařízení ve službě Azure Stack.  

## <a name="physical-device-access-auditing"></a>Auditování přístupu k fyzickým zařízením

Všechny fyzické zařízení ve službě Azure Stack podporují použití TACACS nebo pomocí protokolu RADIUS. Podpora zahrnuje přístup k řadiči pro správu základní desky (BMC) a síťové přepínače.

Řešení Azure Stack se nedodává s protokolem RADIUS nebo TACACS součástí. Ale řešení ověřily pro podporu použití existující RADIUS nebo TACACS řešení na trhu.

Pro RADIUS pouze MSCHAPv2 potvrzená. To představuje nejbezpečnější implementace pomocí protokolu RADIUS.
Poraďte se s OEM dodavatele hardwaru pro povolení TACAS nebo pomocí protokolu RADIUS v zařízení zahrnutých v rámci řešení pro Azure Stack.

## <a name="syslog-forwarding-for-network-devices"></a>Předávání Syslog pro síťová zařízení

Všechny fyzické síťové zařízení ve službě Azure Stack podporují zprávy syslog. Řešení Azure Stack se nedodává s syslog server. Ale zařízení ověřily pro podporu odesílání zpráv do existujícího řešení syslog na trhu.

Cílová adresa syslog je volitelný parametr shromážděných pro nasazení, ale také přidáním po nasazení. Poraďte se s OEM dodavatele hardwaru a nakonfigurujte předávání v síťových zařízeních syslog.

## <a name="next-steps"></a>Další postup

[Zásady údržby](azure-stack-servicing-policy.md)

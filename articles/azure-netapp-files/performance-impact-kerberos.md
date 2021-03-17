---
title: Dopad na výkon protokolu Kerberos na Azure NetApp Files svazcích NFSv 4.1 | Microsoft Docs
description: V této části najdete popis dostupných možností zabezpečení, testovaných vektorů výkonu a očekávaného dopadu na výkon protokolu Kerberos na Azure NetApp Files svazcích NFSv 4.1.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: b-juche
ms.openlocfilehash: e9054f11c8f55e9fe00266840a9f6e257f14e659
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745077"
---
# <a name="performance-impact-of-kerberos-on-azure-netapp-files-nfsv41-volumes"></a>Dopad na výkon protokolu Kerberos na Azure NetApp Files svazcích NFSv 4.1

Azure NetApp Files podporuje [šifrování klienta NFS v](configure-kerberos-encryption.md) režimech Kerberos (krb5, Krb5i a krb5p) pomocí šifrování AES-256. Tento článek popisuje vliv na výkon protokolu Kerberos na svazcích NFSv 4.1. 

## <a name="available-security-options"></a>Dostupné možnosti zabezpečení 

Možnosti zabezpečení, které jsou aktuálně dostupné pro svazky NFSv 4.1, jsou následující: 

* **SEK = sys** používá k ověření operací systému souborů NFS místní identifikátory UID a GIDs AUTH_SYS v systému UNIX.
* **SEK = krb5** používá k ověřování uživatelů protokol Kerberos V5 namísto místních identifikátorů UID a GIDS systému UNIX.
* **SEK = Krb5i** používá protokol Kerberos V5 k ověřování uživatelů a provádí kontrolu integrity operací systému souborů NFS pomocí zabezpečených kontrolních součtů, které zabraňují manipulaci s daty.
* **SEK = krb5p** používá protokol Kerberos V5 pro ověřování uživatelů a kontrolu integrity. Šifruje provoz systému souborů NFS, aby nedocházelo ke sledování provozu. Tato možnost je nejbezpečnější nastavení, ale zahrnuje i nejvyšší nároky na výkon.

## <a name="performance-vectors-tested"></a>Testované vektory výkonu

Tato část popisuje jeden vliv na výkon různých možností na straně klienta `sec=*` .

* Dopad na výkon byl testován na dvou úrovních: nízká Concurrency (nízké zatížení) a vysoká souběžnost (horní meze vstupně-výstupních operací a propustnosti).  
* Byly testovány tři typy úloh:  
    * Nízká operace náhodným čtením/zápisem (pomocí FIO)
    * Sekvenční operace čtení/zápisu velkých operací (pomocí FIO)
    * Metadata vysoké zátěže generované aplikacemi, jako je git

## <a name="expected-performance-impact"></a>Očekávaný dopad na výkon 

Existují dvě oblasti výběru: světlo zatížení a horní mez. Následující seznamy popisují nastavení zabezpečení vlivu na výkon podle nastavení zabezpečení a scénáře podle scénáře. Všechna porovnání jsou provedena proti `sec=sys` parametru zabezpečení. Test byl proveden na jednom svazku s použitím jednoho klienta. 

Dopad na výkon krb5:

* Nízká souběžná (r/w):
    * Sekvenční latence zvýšené o 0,3 MS.
    * Náhodná vstupně-výstupní latence vzrostla 0,2 MS.
    * I/O latence metadat vzrostla 0,2 MS.
* Vysoká souběžnost (r/w): 
    * Krb5 neovlivnila maximální Sekvenční propustnost.
    * Maximální počet náhodných vstupně-výstupních operací se snížil o 30% pro úlohy čistého čtení s celkovým dopadem na hodnotu nula, protože se zatížení posune na čistý zápis. 
    * Maximální zatížení metadat se snížilo na 30%.

Dopad na výkon Krb5i: 

* Nízká souběžná (r/w):
    * Sekvenční latence zvýšené o 0,5 ms.
    * Náhodná vstupně-výstupní latence vzrostla 0,2 MS.
    * I/O latence metadat vzrostla 0,2 MS.
* Vysoká souběžnost (r/w): 
    * Maximální následná propustnost se snížila o 70% celkových bez ohledu na kombinaci úloh.
    * Maximální počet náhodných vstupně-výstupních operací se snížil o 50% pro úlohy čistého čtení s celkovým dopadem snížení na 25% při posunu zátěže na čistý zápis. 
    * Maximální zatížení metadat se snížilo na 30%.

Dopad na výkon krb5p:

* Nízká souběžná (r/w):
    * Sekvenční latence zvýšené o 0,8 ms.
    * Náhodná vstupně-výstupní latence vzrostla 0,2 MS.
    * I/O latence metadat vzrostla 0,2 MS.
* Vysoká souběžnost (r/w): 
    * Maximální následná propustnost se snížila o 85% celkových bez ohledu na kombinaci úloh. 
    * Maximální počet náhodných vstupně-výstupních operací poklesl o 65% pro úlohy čistého čtení s celkovým dopadem snížení na 43% při posunování zatížení na čistý zápis. 
    * Maximální zatížení metadat se snížilo na 30%.

## <a name="next-steps"></a>Další kroky  

* [Konfigurace šifrování Kerberos NFSv4.1 pro Azure NetApp Files](configure-kerberos-encryption.md) 

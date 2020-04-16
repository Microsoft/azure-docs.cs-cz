---
title: Azure Key Vault bezpečnostní světy | Dokumenty společnosti Microsoft
description: Azure Key Vault je služba s více klienty. Používá fond hsm v každé umístění Azure. Všechna umístění v zeměpisné oblasti sdílejí kryptografickou hranici.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 07/03/2017
ms.openlocfilehash: 3584f83c5e1a5e83d069373395227b70c084eae9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428950"
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Azure Key Vault bezpečnostní světy a geografické hranice

Azure Key Vault je služba s více klienty a používá fond modulů hardwarového zabezpečení (HSM) v každém umístění Azure. 

Všechny hmenové názvy v umístění Azure ve stejné geografické oblasti sdílejí stejnou kryptografickou hranici (Thales Security World). Například východní USA a Západní USA sdílejí stejný svět zabezpečení, protože patří do geografické polohy USA. Podobně všechna umístění Azure v Japonsku sdílejí stejný svět zabezpečení a všechna umístění Azure v Austrálii, Indii a tak dále. 

## <a name="backup-and-restore-behavior"></a>Chování zálohování a obnovení

Zálohu odebranou klíči z trezoru klíčů v jednom umístění Azure lze obnovit do trezoru klíčů v jiném umístění Azure, pokud jsou splněny obě tyto podmínky:

- Obě umístění Azure patří do stejné zeměpisné polohy
- Oba trezory klíčů patří ke stejnému předplatnému Azure.

Například záloha přijatá daným předplatným klíče v trezoru klíčů v západní Indii může být obnovena pouze do jiného trezoru klíčů ve stejném předplatném a geografickém umístění. západní Indie, střední Indie nebo jižní Indie.

## <a name="regions-and-products"></a>Regiony a produkty

- [Oblasti Azure](https://azure.microsoft.com/regions/)
- [Produkty společnosti Microsoft podle oblastí](https://azure.microsoft.com/regions/services/)

Oblasti jsou mapovány do světů zabezpečení, které jsou v tabulkách zobrazeny jako hlavní nadpisy:

V článku Produkty podle oblasti například karta **Amerika** obsahuje všechny mapování na oblast Ameriky – východ USA, STŘEDNÍ USA, ZÁPADNÍ USA. 

>[!NOTE]
>Výjimkou je, že US DOD EAST a US DOD CENTRAL mají své vlastní bezpečnostní světy. 

Podobně na kartě **Evropa** se severní Evropa i ZÁPADNÍ EVROPA mapují na evropský region. Totéž platí i na kartě **Asie a Tichomoří.**




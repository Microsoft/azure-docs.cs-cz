---
title: Azure Key Vault zabezpečení světů | Microsoft Docs
description: Azure Key Vault je služba s více klienty. V každém umístění Azure používá fond HSM. Všechna místa v geografické oblasti sdílí kryptografickou hranici.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 07/03/2017
ms.openlocfilehash: d21deea13aac3d40c452a183c340d3108a1a01f4
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936324"
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Azure Key Vault světů zabezpečení a geografické hranice

Azure Key Vault je víceklientské služby a v každém umístění Azure používá fond modulů hardwarového zabezpečení (HSM). 

Všechny HSM v umístěních Azure ve stejné geografické oblasti sdílejí stejnou šifrovací hranici (Thales Security World). Například Východní USA a Západní USA sdílet stejný svět zabezpečení, protože patří do geografického umístění USA. Podobně všechna umístění Azure v Japonsku sdílejí stejný bezpečnostní svět a všechna umístění Azure v Austrálii, Indii a tak dále. 

## <a name="backup-and-restore-behavior"></a>Chování zálohování a obnovení

Záloha klíče z trezoru klíčů v jednom umístění Azure se dá obnovit do trezoru klíčů v jiném umístění Azure, pokud jsou splněné obě tyto podmínky:

- Obě umístění Azure patří do stejného geografického umístění.
- Oba trezory klíčů patří do stejného předplatného Azure.

Například záloha pořízená daným předplatným klíče v trezoru klíčů v Západní Indie může být obnovena pouze do jiného trezoru klíčů ve stejném předplatném a geografickém umístění. Západní Indie, Střed Indie nebo Jižní Indie.

## <a name="regions-and-products"></a>Oblasti a produkty

- [Oblasti Azure](https://azure.microsoft.com/regions/)
- [Produkty společnosti Microsoft podle oblastí](https://azure.microsoft.com/regions/services/)

Oblasti se mapují na světů zabezpečení, které se zobrazují jako hlavní nadpisy v tabulkách:

V článku produkty podle oblasti obsahuje například karta **Severní Amerika** , USA – východ, střed USA, západní USA, všechna mapování na oblast v Severní Americe. 

>[!NOTE]
>Výjimkou je to, že US DOD a USA – střed mají vlastní světů zabezpečení. 

Podobně jsou na kartě **Evropa** v oblasti Severní Evropa a západní Evropa namapovány na oblast Evropa. Totéž platí také na kartě **Asie a Tichomoří** .

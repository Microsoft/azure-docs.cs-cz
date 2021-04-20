---
title: Azure Key Vault zabezpečení světů | Microsoft Docs
description: Azure Key Vault je služba s více klienty. V každé oblasti Azure používá fond HSM. Všechny oblasti v geografické oblasti sdílejí určitou šifrovací hranici.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 07/03/2017
ms.openlocfilehash: 0d82a3cb4c08d47b6827072378b9827037d32412
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751809"
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Azure Key Vault světů zabezpečení a geografické hranice

Produkty Azure jsou dostupné v řadě geografických oblastí [Azure](https://azure.microsoft.com/en-us/global-infrastructure/geographies/), přičemž každé geografické oblasti Azure obsahuje jednu nebo více oblastí. Například zeměpisná oblast Evropy obsahuje dvě oblasti – Severní Evropa a Západní Evropa--zatímco jedinou oblastí v Brazílii Brazílie je Brazílie – jih.

Azure Key Vault je víceklientská služba, která používá fond modulů hardwarového zabezpečení (HSM). Všechny HSM v geografickém umístění sdílejí stejnou kryptografickou hranici, která se označuje jako "Security World". Každé geografické prostředí odpovídá jedinému světovému zabezpečení a naopak.

Východní USA a Západní USA sdílejí stejný bezpečnostní svět, protože patří do geografické oblasti (USA). Podobně všechny oblasti Azure v Japonsku sdílejí stejný bezpečnostní svět, stejně jako všechny oblasti Azure v Austrálii a tak dále.

>[!NOTE]
> Výjimkou je to, že US DOD a USA – střed mají vlastní světů zabezpečení.

## <a name="backup-and-restore-behavior"></a>Chování zálohování a obnovení

Zálohování klíče z trezoru klíčů v jedné oblasti Azure může být obnoveno do trezoru klíčů v jiné oblasti Azure, pokud jsou splněny obě tyto podmínky:

- Obě oblasti Azure patří do stejné geografické oblasti.
- Oba trezory klíčů patří do stejného předplatného Azure.

Například zálohování klíče v trezoru klíčů Západní Indie lze obnovit do jiného trezoru klíčů ve stejném předplatném v oblasti Indie (Západní Indie, Střed Indie a Jižní Indie oblasti).

## <a name="next-steps"></a>Další kroky

- Zobrazit [produkty společnosti Microsoft podle oblastí](https://azure.microsoft.com/regions/services/)

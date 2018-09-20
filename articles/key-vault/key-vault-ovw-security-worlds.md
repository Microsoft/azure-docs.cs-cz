---
ms.assetid: ''
title: Světy zabezpečení služby Azure Key Vault | Dokumentace Microsoftu
ms.service: key-vault
ms.topic: conceptual
author: bryanla
ms.author: bryanla
manager: mbaldwin
ms.date: 07/03/2017
ms.openlocfilehash: 13470e85e4fe2741a11fcade3b97d333eb03efb7
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46465892"
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Světy zabezpečení služby Azure Key Vault a geografické hranice

Služba Azure Key Vault je služba pro více tenantů a používá fond modulů hardwarového zabezpečení (HSM) v jednotlivých oblastech Azure. 

Všechny moduly hardwarového zabezpečení v Azure umístění ve stejné zeměpisné oblasti sdílet stejnou hranici kryptografických (celý svět zabezpečení společnosti Thales). USA – východ a USA – západ sdílet stejné architektury security world, protože náleží do USA geografické polohy. Obdobně všech umístěních Azure v Japonsku sdílejí stejné architektury security world a všech umístěních Azure v Austrálii, Indie a tak dále. 

## <a name="backup-and-restore-behavior"></a>Zálohování a obnovení chování

Backup pořídí klíče z trezoru klíčů na jednom místě Azure možné obnovit do služby key vault do jiného umístění Azure, dokud jsou splněny obě tyto podmínky:

- Obě lokality Azure patří do stejného geografického umístění
- Obě trezorům klíčů patří do stejného předplatného Azure

Například zálohování provedenou na základě dané předplatné klíče ve službě key vault v oblasti Západní Indie, se dají obnovit jen do jiného trezoru klíčů ve stejném předplatném a geografického umístění. Západní Indie, střed Indie nebo Indie – jih.

## <a name="regions-and-products"></a>Oblasti a produkty

- [Oblasti Azure](https://azure.microsoft.com/regions/)
- [Microsoft produkty v jednotlivých oblastech](https://azure.microsoft.com/regions/services/)

Oblasti jsou mapovány na světy zabezpečení, zobrazí jako hlavní záhlaví v tabulkách:

V produktech v oblasti článku, například **Americas** karta obsahuje VÝCHOD USA, STŘED USA, ZÁPADNÍ USA veškerá mapování do oblasti Severní a Jižní Americe. 

>[!NOTE]
>Výjimkou je, že US DOD – VÝCHOD a US DOD – STŘED mají své vlastní světy zabezpečení. 

Podobně na **Evropa** kartu, severní EVROPA a ZÁPADNÍ EVROPA, obě namapovány na oblasti Evropa. Totéž platí také na **Asie a Tichomoří** kartu.




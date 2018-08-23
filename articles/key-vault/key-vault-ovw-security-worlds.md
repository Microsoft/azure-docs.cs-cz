---
ms.assetid: ''
title: Světy zabezpečení služby Azure Key Vault | Dokumentace Microsoftu
ms.service: key-vault
author: bryanla
ms.author: bryanla
manager: mbaldwin
ms.date: 07/03/2017
ms.openlocfilehash: 7fd7357a317d5059d6169de2c1536568a254e016
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2018
ms.locfileid: "42054625"
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




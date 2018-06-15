---
title: Verze Key Vault
description: V různých verzích Azure Key Vault
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e8622dcc-59a3-4f4b-9f63-cd2232515a65
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: beb73be66f36ccf95fe27d4d8128106cd12722a8
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012072"
---
# <a name="key-vault-versions"></a>Verze Key Vault

## <a name="2016-10-01---managed-storage-account-keys"></a>2016-10-01 - klíče účtu úložiště spravovaný

Léto 2017 – funkce klíče účtu úložiště přidat jednodušší integraci s Azure Storage. Naleznete v tématu Přehled o další informace najdete [spravované klíče účtu úložiště – přehled](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys).

## <a name="2016-10-01---soft-delete"></a>2016-10-01 - konfigurace soft odstranění

Léto 2017 - funkci soft odstranění přidat pro ochranu dat vylepšené trezorů klíčů a klíče trezoru objekty. Naleznete v tématu Přehled o další informace najdete [Přehled konfigurace Soft odstranění](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).

## <a name="2015-06-01---certificate-management"></a>2015-06-01 - správy certifikátů

Správa certifikátů vkládá jako funkce GA verze 2015-06-01 26 září 2016.

## <a name="2015-06-01---general-availability"></a>2015-06-01 - obecné dostupnosti

Obecné dostupnosti verze 2015-06-01, oznámeny na 24. června 2015.

V této verzi byly provedeny následující změny:

- Odstranění klíče – "použití" pole odebrat.
- Získání informací o klíči - "použití" pole odebrat.
- Import klíče do trezoru – "použití" pole odebrat.
- Obnovení klíče – "použití" pole odebrat.
- Změněné "RSA_OAEP" na "RSA-OAEP" pro algoritmy RSA. V tématu [informace o klíčích, tajných klíčů a certifikátů](about-keys-secrets-and-certificates.md).

## <a name="2015-02-01-preview"></a>2015-02-01-preview 

Druhý preview verze 2015-02-01-preview, oznámeny na 20 duben 2015. Další informace najdete v tématu [aktualizace rozhraní API REST](http://blogs.technet.com/b/kv/archive/2015/04/20/empty-3.aspx) příspěvku na blogu.

Následující úlohy byly aktualizovány:

- Seznam klíčů v trezoru - přidané stránkování podporu pro operaci.
- Výpis verzí klíče - přidat operaci do seznamu verzí klíče.
- Výpis tajných klíčů v trezoru - podpora přidané stránkování.
- Seznam verzí tajného klíče – přidání operace výpis verzí tajného klíče.
- Všechny operace - přidat vytvořit nebo aktualizovat časová razítka na atributy.
- Vytvoření tajného klíče - přidat Content-Type na tajných klíčů.
- Vytvořte klíč - přidat značky jako volitelné informace.
- Vytvoření tajného klíče - přidat značky jako volitelné informace.
- Aktualizace klíče - přidat značky jako volitelné informace.
- Aktualizovat tajný klíč - přidat značky jako volitelné informace.
- Změnit maximální velikost pro tajné klíče z 10 tisíc na 25 kB. Zobrazit, [informace o klíčích, tajných klíčů a certifikátů](about-keys-secrets-and-certificates.md).

## <a name="2014-12-08-preview"></a>2014-12-08-preview

První preview verze 2014-12-08-preview, oznámeny na 8 leden 2015.

## <a name="see-also"></a>Další informace najdete v tématech
- [Informace o klíčích, tajných kódech a certifikátech](about-keys-secrets-and-certificates.md)

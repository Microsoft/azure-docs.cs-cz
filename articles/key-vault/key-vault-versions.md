---
title: Verze Key Vaultu
description: Různé verze služby Azure Key Vault
services: key-vault
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: e452313934c6a3076a3801b70019048090f2c6d1
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64685446"
---
# <a name="key-vault-versions"></a>Verze Key Vaultu

## <a name="2016-10-01---managed-storage-account-keys"></a>2016-10-01 - spravovaných klíčů účtu úložiště

Léto 2017 – funkce klíče účtu úložiště přidat jednodušší integraci s Azure Storage. Naleznete v tématu Přehled najdete [přehled spravovaných klíčů účtu úložiště](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys).

## <a name="2016-10-01---soft-delete"></a>2016-10-01obnovitelného odstranění

Léto 2017 – funkce obnovitelného odstranění do lepší datové ochrany vašich trezorů klíčů a trezor klíčů objektů. Naleznete v tématu Přehled najdete [přehled obnovitelného odstranění](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).

## <a name="2015-06-01---certificate-management"></a>2015-06-01 - Správa certifikátů

Správa certifikátů se přidá jako funkce do všeobecně dostupné verze 2015-06-01 v 26. Zářím 2016.

## <a name="2015-06-01---general-availability"></a>2015-06-01 - všeobecné dostupnosti

Obecná dostupnost verze 2015-06-01, zveřejněných na 24. června 2015.

V této verzi byly provedeny následující změny:

- Odstranění klíče – "použijte" pole odebrat.
- Získejte informace o klíči – "pomocí" pole odebrat.
- Import klíče do trezoru – "použijte" pole odebrat.
- Obnovení klíče – "použijte" pole odebrat.
- Změněné "RSA_OAEP" k "RSA-OAEP" algoritmů RSA. Zobrazit [informace o klíčích, tajných kódů a certifikátů](about-keys-secrets-and-certificates.md).

## <a name="2015-02-01-preview"></a>2015-02-01-preview 

Druhý preview verze 2015-02-01-preview, zveřejněných na 20. dubna 2015. Další informace najdete v tématu [aktualizace rozhraní API REST](https://blogs.technet.com/b/kv/archive/2015/04/20/empty-3.aspx) blogový příspěvek.

Byly aktualizovány následující úkoly:

- Výpis klíčů v trezoru – podpora přidání stránkování k operaci.
- Výpis verzí klíče - přidat operaci do seznamu verzí klíče.
- Výpis tajných kódů v trezoru – podpora přidání stránkování.
- Výpis verzí tajného kódu – přidání operace výpis verzí tajného kódu.
- Všechny operace – přidá do atributy vytvořené nebo aktualizované časová razítka.
- Vytvoření tajného klíče – přidání Content-Type k tajným kódům.
- Vytvoření klíče - přidat značky jako volitelné informace.
- Vytvoření tajného klíče - přidat značky jako volitelné informace.
- Aktualizace klíče - přidat značky jako volitelné informace.
- Aktualizace tajného kódu – přidat značky jako volitelné informace.
- Změnit maximální velikost pro tajné klíče z 10 tisíc na 25 kB. Zobrazit, [informace o klíčích, tajných kódů a certifikátů](about-keys-secrets-and-certificates.md).

## <a name="2014-12-08-preview"></a>2014-12-08-preview

První verze preview verze 2014-12-08-preview, zveřejněných na 8. ledna 2015.

## <a name="see-also"></a>Další informace najdete v tématech
- [Informace o klíčích, tajných kódech a certifikátech](about-keys-secrets-and-certificates.md)

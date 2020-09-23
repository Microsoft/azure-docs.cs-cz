---
title: Povolení přístupu k úložišti prostřednictvím výjimky důvěryhodné služby
titleSuffix: Azure Cognitive Search
description: Návod, jak nastavit výjimku důvěryhodné služby pro zabezpečený přístup k datům z účtů úložiště.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 1400f3c3d15698a5f1a145e8e0750ad7c4e9cec8
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90971403"
---
# <a name="accessing-data-in-storage-accounts-securely-via-trusted-service-exception"></a>Zabezpečený přístup k datům v účtech úložiště pomocí výjimky důvěryhodné služby

Indexery, které přistupují k datům v účtech úložiště, můžou využít funkci [výjimky důvěryhodné služby](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) k zabezpečení přístupu k datům. Tento mechanismus nabízí zákazníkům, kteří nemůžou udělit [přístup k indexeru prostřednictvím pravidel brány firewall protokolu IP](search-indexer-howto-access-ip-restricted.md) , jednoduchou, zabezpečenou a bezplatnou alternativou pro přístup k datům v účtech úložiště.

## <a name="step-1-configure-connection-to-the-storage-account-via-identity"></a>Krok 1: Konfigurace připojení k účtu úložiště prostřednictvím identity

Při konfiguraci indexerů pro přístup k účtům úložiště prostřednictvím spravované identity vyhledávací služby postupujte podle podrobných informací uvedených v [příručce pro Managed identity Access](search-howto-managed-identities-storage.md) .

## <a name="step-2-allow-trusted-microsoft-services-to-access-the-storage-account"></a>Krok 2: povolení přístupu k účtu úložiště důvěryhodným službám Microsoftu

V Azure Portal přejděte na kartu brány firewall a virtuální sítě v účtu úložiště. Ujistěte se, že je zaškrtnutá možnost "při přístupu k tomuto účtu úložiště použít možnost" udělit důvěryhodným službám Microsoftu ". Tato možnost umožní účtu úložiště (silnému ověřování) povolit přístup k datům v účtu úložiště, a to i v případě, že je zabezpečený pomocí pravidel brány firewall protokolu IP.

![Výjimka důvěryhodné služby](media\search-indexer-howto-secure-access\exception.png "Výjimka důvěryhodné služby")

Indexery teď budou mít přístup k datům v účtu úložiště, a to i v případě, že je účet zabezpečený pomocí pravidel brány firewall protokolu IP.

## <a name="next-steps"></a>Další kroky

Další informace o Azure Storage indexerech:

- [Indexer Azure Blob](search-howto-indexing-azure-blob-storage.md)
- [Azure Data Lake Storage Gen2 indexer](search-howto-index-azure-data-lake-storage.md)
- [Indexer tabulek Azure](search-howto-indexing-azure-tables.md)

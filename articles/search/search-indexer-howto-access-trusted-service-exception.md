---
title: Přístup indexeru k Azure Storage pomocí výjimky důvěryhodné služby
titleSuffix: Azure Cognitive Search
description: Povolte přístup k datům indexerem v Azure Kognitivní hledání k zabezpečeným datům uloženým v Azure Storage.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: e139c15ef6de00376a4e1a88000d263c3486994b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92101371"
---
# <a name="indexer-access-to-azure-storage-using-the-trusted-service-exception-azure-cognitive-search"></a>Přístup indexeru k Azure Storage pomocí výjimky důvěryhodné služby (Azure Kognitivní hledání)

Indexery ve službě Azure Kognitivní hledání, které přistupují k datům v Azure Storage účty, můžou využít funkci [výjimky důvěryhodné služby](../storage/common/storage-network-security.md#exceptions) k zabezpečení přístupu k datům. Tento mechanismus nabízí zákazníkům, kteří nemůžou udělit [přístup k indexeru pomocí pravidel brány firewall protokolu IP](search-indexer-howto-access-ip-restricted.md) , jednoduchou, zabezpečenou a bezplatnou alternativou pro přístup k datům v účtech úložiště.

> [!NOTE]
> Podpora přístupu k datům v účtech úložiště prostřednictvím důvěryhodné služby je omezená na úložiště objektů BLOB v Azure a Azure Data Lake Gen2 úložiště. Služba Azure Table Storage není podporována.

## <a name="step-1-configure-a-connection-using-a-managed-identity"></a>Krok 1: Konfigurace připojení pomocí spravované identity

Postupujte podle pokynů v části [nastavení připojení k účtu Azure Storage pomocí spravované identity](search-howto-managed-identities-storage.md). Až budete hotovi, zaregistrujte svou vyhledávací službu pomocí Azure Active Directory jako důvěryhodnou službu a budete mít udělená oprávnění v Azure Storage, která pro přístup k datům nebo informacím udělí konkrétní práva pro identitu vyhledávání.

## <a name="step-2-allow-trusted-microsoft-services-to-access-the-storage-account"></a>Krok 2: povolení přístupu k účtu úložiště důvěryhodným službám Microsoftu

V Azure Portal přejděte na kartu **brány firewall a virtuální sítě** v účtu úložiště. Ujistěte se, že je zaškrtnutá možnost **pro přístup k tomuto účtu úložiště možnost Allow Trusted Services Microsoft** . Tato možnost umožní účtu úložiště (silnému ověřování) povolit přístup k datům v účtu úložiště, a to i v případě, že je zabezpečený pomocí pravidel brány firewall protokolu IP.

![Výjimka důvěryhodné služby](media\search-indexer-howto-secure-access\exception.png "Výjimka důvěryhodné služby")

Indexery teď budou mít přístup k datům v účtu úložiště, a to i v případě, že je účet zabezpečený pomocí pravidel brány firewall protokolu IP.

## <a name="next-steps"></a>Další kroky

Další informace o Azure Storage indexerech:

- [Indexer Azure Blob](search-howto-indexing-azure-blob-storage.md)
- [Azure Data Lake Storage Gen2 indexer](search-howto-index-azure-data-lake-storage.md)
- [Indexer tabulek Azure](search-howto-indexing-azure-tables.md)
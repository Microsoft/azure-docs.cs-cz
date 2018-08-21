---
title: Ověření balíčky výrobce OEM (OEM) při ověřování služby Azure Stack jako služba | Dokumentace Microsoftu
description: Zjistěte, jak zkontrolovat balíčky výrobce OEM (OEM) s ověřením jako služba.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: a08f439780e0080d8da2cde1531e1580dbdad14f
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235272"
---
# <a name="validate-oem-packages"></a>Ověření balíčky výrobce OEM

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Nový balíček pro výrobce OEM můžete otestovat, pokud došlo ke změně firmware a ovladače pro ověření dokončeného řešení. Pokud váš balíček uplynutí testu, je podepsán společností Microsoft. Váš test musí obsahovat aktualizovaný balíček rozšíření výrobcem OEM s ovladače a firmware, které prošly logo systému Windows Server a počítačů s testy.

Všechny testy dokončit během 24 hodin s výsledkem **úspěšné**. Pokud některý z testů jako výsledek **nepovedlo**, oznámit chybu v [Microsoft Collaborate](https://aka.ms/collaborate) a uvědomit společnost Microsoft odesláním e-mailu [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

## <a name="get-your-oem-package-signed"></a>Získání balíčku výrobce OEM podepsané

1. Ujistěte se, že byl použit aktuální měsíční aktualizace. Nejnovější verze najdete v nejnovější verzi [dokumentace operátora k Azure Stack > Přehled > zpráva k vydání verze](https://docs.microsoft.com/azure/azure-stack/) .

    Aktualizace softwaru společnosti Microsoft do služby Azure Stack jsou určeny pomocí zásady vytváření názvů, například 1803 označující aktualizace je pro. března 2018. Informace o aktualizaci zásad služby Azure Stack, tempo a verze poznámky jsou k dispozici, najdete v článku [Azure Stack zásady obsluhy](https://docs.microsoft.com/azure/azure-stack/azure-stack-servicing-policy).

1. Kontrola stavu systému spuštěním **testovací AzureStack** podle [spustit ověřovací test pro službu Azure Stack. Opravte jakékoli upozornění a chyby před spuštěním žádné testy.

2. V [portálu ověření](https://azurestackvalidation.com), vyberte existující řešení. Pokud jste nepřidali vašeho řešení, přečtěte si téma [přidání nových řešení](azure-stack-vaas-validate-solution-new.md#add-a-new-solution).

3. Vyberte **Start** na **ověření balíčku** dlaždici můžete spustit nový pracovní postup.

    ![Ověření balíčku](media/image3.png)

4.  Zadejte připojovací řetězec Diagnostika. Pokyny najdete v tématu [nastavit účet úložiště](azure-stack-vaas-set-up-account.md).

    Pro každé spuštění ověřování balíčku je nutné zadat balíček rozšíření pro výrobce OEM. Zadejte balíček výrobce OEM, která byla nainstalována na řešení v době nasazení Azure stacku. Pokyny najdete v tématu [vytvoření objektu blob služby Azure storage k ukládání protokolů](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs).

    Soubor JSON s proměnnými prostředí musí použít na dokončení vstupu u povinných polí pro spuštění, aby se zabránilo chybám při zadávání dat. Pokyny najdete v tématu [získat konfiguračním souboru v nasazení služby Azure Stack](azure-stack-vaas-parameters.md).

5. Spusťte testy.

6. Pokud všechny testy byly úspěšně dokončeny, Odeslat název vašeho řešení a balíček validace [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) na žádost o podepsání balíčku.

## <a name="next-steps"></a>Další postup

- Další informace o [ověřování služby Azure Stack jako služba](https://docs.microsoft.com/azure/azure-stack/partner).

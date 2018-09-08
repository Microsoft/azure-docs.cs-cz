---
title: Ověření aktualizace softwaru od Microsoftu v Azure stacku ověření jako služba | Dokumentace Microsoftu
description: Zjistěte, jak ověřit aktualizací softwaru od Microsoftu pomocí služby ověřování jako služba.
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
ms.openlocfilehash: 6ef8c0486a694ac44c53375b24893812b10343e4
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158479"
---
# <a name="validate-software-updates-from-microsoft"></a>Ověření aktualizace softwaru od Microsoftu

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft pravidelně vydá aktualizace softwaru Azure Stack. Tyto aktualizace jsou k dispozici pro partnery společné technikům Azure Stack s předstihem prováděné veřejně dostupné, takže můžete ověřit aktualizací před jejich řešení a zasílat připomínky do Microsoftu.

## <a name="test-an-existing-solution"></a>Testování existujícího řešení

1. Přihlaste se k [portálu ověření](https://azurestackvalidation.com).

2. Vyberte existující řešení, ve kterém aktualizace od společnosti Microsoft byla nasazena a vyberte **Start** na **ověřování balíčku** dlaždici.

    ![Ověření balíčku](media/image3.png)

3. Zadejte název ověření.

4. Zadejte adresu URL, která byla nainstalována na řešení v době nasazení balíčku výrobce OEM. Použijte adresu URL pro balíček uložené ve službě Azure blob service. Další informace najdete v tématu [vytvoření objektu blob služby Azure storage k ukládání protokolů](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs).

5. Vyberte **nahrát** přidat konfigurační soubor nasazení. Odkazovat [ověřování nové řešení pro Azure Stack](azure-stack-vaas-validate-solution-new.md) informace o nahrávání konfigurační soubor nasazení.

6. Konfigurační soubor nasazení pak musíte přizpůsobit pomocí souboru parametrů správné prostředí, najdete v článku [parametry prostředí](azure-stack-vaas-parameters.md#environment-parameters) další podrobnosti.

    > [!Note]   
    > Konfigurační soubor nasazení je dále přizpůsobit přidáním společné parametry testu. Další informace najdete v tématu [společných parametrů pracovních postupů pro ověřování služby Azure Stack jako služba](azure-stack-vaas-parameters.md)

7. Uživatelské jméno a heslo pro tenanta uživatele, Správce služby a správu cloudu je třeba zadat ručně.

8. Zadejte adresu URL do objektu blob Azure Storage k ukládání diagnostických protokolů. Další informace najdete v tématu [vytvoření objektu blob služby Azure storage k ukládání protokolů](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs).

    > [!Note]  
    > K označení pracovního postupu můžete zadat popisná klíčová slova.

10. Vyberte **odeslat** uložit pracovní postup.

Pracovní postup řešení spouští přibližně 24 hodin. Přidáte odkaz na nebo instrukce na plánování testů. Zrušte v nástroji.

Najít další informace o sledování průběhu ověření spustit, najdete v článku [sledovat testovací ](azure-stack-vaas-monitor-test.md).

## <a name="next-steps"></a>Další postup

- Další informace o [ověřování služby Azure Stack jako služba](https://docs.microsoft.com/azure/azure-stack/partner).

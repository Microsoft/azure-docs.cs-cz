---
title: Lístek podpory protokolu pro Azure Stack Edge pro, Azure Data Box Gateway | Microsoft Docs
description: Naučte se, jak protokolovat žádosti o problémy související s Azure Stack hraniční verze pro nebo Data Box Gateway objednávky.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: f76652600f42d7e82914836537935ac9a74decb4
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102436786"
---
# <a name="open-a-support-ticket-for-azure-stack-edge-pro-and-azure-data-box-gateway"></a>Otevření lístku podpory pro Azure Stack Edge pro a Azure Data Box Gateway

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-databox-gateway-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-databox-gateway-sku.md)]

Tento článek se týká Azure Stack Edge pro a Azure Data Box Gateway, které jsou spravovány službou Azure Stack Edge pro/Azure Data Box Gateway. Pokud narazíte na nějaké problémy se službou, můžete vytvořit žádost o službu pro technickou podporu. Tento článek vás provede:

* Jak vytvořit žádost o podporu.
* Jak spravovat životní cyklus žádosti o podporu v rámci portálu.

## <a name="create-a-support-request"></a>Vytvoření žádosti o podporu

Chcete-li vytvořit žádost o podporu, postupujte podle následujících kroků:

1. Přejít na Azure Stack Edge pro nebo Data Box Gateway objednávky. Přejděte do části **Podpora a řešení potíží** a pak vyberte **Nová žádost o podporu**.

2. V části **Nová žádost o podporu** na kartě **základy** proveďte následující kroky:

    1. V rozevíracím seznamu **typ problému** vyberte možnost **technický**.
    2. Vyberte své **předplatné**.
    3. V části **Služba** zaškrtněte **moje služby**. V rozevíracím seznamu vyberte **Azure Stack Edge pro a data box Gateway**.
    4. Vyberte **prostředek**. To odpovídá názvu vaší objednávky.
    5. Stručné **Shrnutí** problému, ke kterému dochází. 
    6. Vyberte **typ problému**.
    7. Na základě vybraného typu problému vyberte odpovídající podtyp **problému**.
    8. Vyberte **Další: řešení >>**.

        ![Základy](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-1.png)

3. Na kartě **Podrobnosti** proveďte následující kroky:

    1. Zadejte datum a čas spuštění problému.
    2. Zadejte **Popis** problému.
    3. V části **nahrání souboru** vyberte ikonu složky a procházejte všechny další soubory, které chcete nahrát.
    4. Podívejte se na **sdílené diagnostické informace**.
    5. **Plán podpory** se vyplní automaticky na základě vašeho předplatného.
    6. V rozevíracím seznamu vyberte **závažnost**.
    7. Zadejte **preferovanou metodu kontaktu**.
    8. **Hodiny odezvy** se automaticky vyberou podle vašeho plánu předplatného.
    9. Zadejte jazyk, pro který chcete podporu.
    10. V **kontaktní informace** zadejte své jméno, e-mail, telefon, volitelný kontakt, zemi nebo oblast. Podpora Microsoftu tyto informace využívá k tomu, abyste mohli získat další informace, diagnostiku a řešení. 
    11. Vyberte **Další: zkontrolovat + vytvořit >>**.

        ![Problém](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-2.png)

4. Na kartě **Revize + vytvořit** si projděte informace týkající se lístku podpory. Vyberte **Vytvořit**. 

    ![Problém 2](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-3.png)

    Po vytvoření lístku podpory vás pracovník podpory vás bude kontaktovat, jakmile bude možné pokračovat ve vaší žádosti.

## <a name="get-hardware-support"></a>Získat hardwarovou podporu

Tyto informace se vztahují jenom na zařízení Azure Stack. Postup hlášení hardwarových problémů je následující:

1. Otevřete lístek podpory z Azure Portal pro hardwarový problém. V části **typ problému** vyberte **Azure Stack hardware**. Vyberte **podtyp problému** jako **selhání hardwaru**.

    ![Problém s hardwarem](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-hardware-issue-1.png)

    Po vytvoření lístku podpory vás pracovník podpory vás bude kontaktovat, jakmile bude možné pokračovat ve vaší žádosti.

2. Pokud podpora Microsoftu zjistí, že se jedná o problém s hardwarem, nastane jedna z následujících akcí:

    * Je odeslána jednotka pro nahrazení pole (FRU) pro neúspěšnou součást hardwaru. V současné době jsou k dispozici pouze jednotky napájení a jednotky SSD (Solid-State Drive), které podporuje FRUs.
    * V rámci následujícího pracovního dne se nahradí jenom FRUs, všechno ostatní vyžaduje, aby se dodalo úplné nahrazení systému (FSR).

3. Pokud je určena náhrada jednotky FRU, kterou vyžaduje 1 – místní čas (od pondělí do pátku), technik na pracovišti odešle další pracovní den do svého umístění a provede náhradu jednotky FRU. Úplné nahrazení systému obvykle trvá mnohem déle, protože tyto součásti se dodávají z našeho továrního řízení a můžou podléhat přepravě a omezením celních zpoždění.

## <a name="manage-a-support-request"></a>Správa žádosti o podporu

Po vytvoření lístku můžete spravovat lístek v celém jeho životním cyklu přímo z portálu.

### <a name="to-manage-your-support-requests"></a>Správa žádostí o podporu

1. Pokud se chcete dostat na stránku Nápověda a podpora, přejděte na **Procházet a přejděte > nápovědu a podpora**.

    ![Správa žádostí o podporu](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-manage-support-request-1.png)

2. V **nápovědě a podpoře** se zobrazí tabulkový výpis **posledních žádostí o podporu** .

    <!--[Manage support requests](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-1.png)--> 

3. Vyberte a klikněte na žádost o podporu. Můžete zobrazit stav a podrobnosti této žádosti. Pokud chcete pokračovat v této žádosti, klikněte na **+ Nová zpráva** .

## <a name="next-steps"></a>Další kroky

Naučte se [řešit problémy související s Azure Stack Edge pro](azure-stack-edge-troubleshoot.md).
Naučte se [řešit problémy související s data box Gateway](../databox-gateway/data-box-gateway-troubleshoot.md).
---
title: Postup zachovat konstantní virtuální IP adresy pro cloudové služby Azure | Microsoft Docs
description: Zjistěte, jak zajistit, že virtuální IP adresy (VIP) cloudové služby Azure nemění.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 4a58e2c6-7a79-4051-8a2c-99182ff8b881
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: c02e80a97fe5943019f446299e370bf029a01ff4
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="retain-a-constant-virtual-ip-address-for-an-azure-cloud-service"></a>Zachovat konstantní virtuální IP adresy pro cloudové služby Azure
Při aktualizaci Cloudová služba, která je hostovaná v Azure, musíte může zajistit, že virtuální adresy IP (VIP) služby nemění. Mnoho služby domény pro použití v systému DNS (Domain Name) pro registraci názvů domén. DNS funguje pouze v případě, že virtuální IP adresa zůstala stejná. Můžete použít **Průvodci publikováním** v nástroje Azure k zajištění, že VIP cloudové služby nezmění. když ho aktualizujete. Další informace o tom, jak používat správu domény DNS pro cloudové služby najdete v tématu [konfigurace vlastního názvu domény pro cloudové služby Azure](cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="publish-a-cloud-service-without-changing-its-vip"></a>Publikování Cloudová služba beze změny jeho VIP
Virtuální IP adresu cloudové služby je přidělen při první jeho nasazení do Azure v konkrétní prostředí, například v provozním prostředí. Virtuální IP adresa změní jenom v případě explicitně odstranit nasazení nebo nasazení je implicitně odstraní proces nasazení aktualizace. Uchovat virtuální IP adresu, nesmí odstraňte nasazení, a ujistěte se, že Visual Studio automaticky neodstraní vaše nasazení. 

Můžete zadat nastavení nasazení **Průvodci publikováním**, který podporuje několik možností nasazení. Můžete zadat nové nasazení nebo nasazení aktualizace, která může být přírůstkové nebo souběžných. Oba typy nasazení aktualizace zachovat virtuální IP adresu. Definice těchto různých typů nasazení naleznete v tématu [Průvodci publikováním aplikace Azure](vs-azure-tools-publish-azure-application-wizard.md). Kromě toho můžete řídit, jestli předchozích nasazení cloudové služby je odstraněna, když dojde k chybě. Pokud tato možnost není správně nastavený, může se neočekávaně měnit virtuální IP adresu.

## <a name="update-a-cloud-service-without-changing-its-vip"></a>Aktualizace cloudové služby, aniž byste museli měnit jeho VIP
1. Vytvořit nebo otevřít projekt Azure cloud service v sadě Visual Studio. 

2. V **Průzkumníku**, klikněte pravým tlačítkem na projekt. V místní nabídce vyberte **publikovat**.

    ![Publikování nabídky](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/solution-explorer-publish-menu.png)

3. V **publikování aplikaci Azure** dialogové okno pole, vyberte předplatné Azure, ke které chcete nasadit. Přihlášení, pokud potřebné a vyberte **Další**.

    ![Publikování Azure aplikace přihlašovací stránku](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-signin.png)

4. Na **společná nastavení** ověřte, který služby název cloudu, do které nasazujete, **prostředí**, **konfigurace sestavení**a **konfigurace služby** správně.

    ![Publikování karta společné nastavení aplikace Azure](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-common-settings.png)

5. Na **Upřesnit nastavení** ověřte, že **označení nasazení** a **účet úložiště** jsou správné. Ověřte, zda **odstranění nasazení na selhání** políčko není zaškrtnuté a ověřte, že **nasazení aktualizace** je zaškrtnuté políčko. Zrušením **odstranění nasazení na selhání** zaškrtávací políčko, zajistíte, že vaše virtuální IP adresy není ke ztrátě, pokud dojde k chybě během nasazení. Výběrem **aktualizaci nasazení** zaškrtávací políčko můžete zajistěte, aby vaše nasazení není odstraněn a vaše virtuální IP adresy není ztraceny po publikování aplikace. 

    ![Publikování karta Upřesnit nastavení aplikace Azure](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-advanced-settings.png)

6. Chcete-li dále určit, jakým způsobem chcete role aktualizovat, vyberte **nastavení** vedle **nasazení aktualizace**. Vyberte buď **přírůstkové aktualizace** nebo **Souběžná aktualizace**a vyberte **OK**. Zvolte **přírůstkové aktualizace** aktualizovat každou instanci vaší aplikace, na jeden po druhém, a to tak, aby aplikace je vždy k dispozici. Zvolte **Souběžná aktualizace** aktualizovat všechny instance aplikace ve stejnou dobu. Souběžné aktualizace je rychlejší, ale vaše služba nemusí být k dispozici během procesu aktualizace. Po dokončení vyberte **Další**.

    ![Publikovat stránku nastavení nasazení aplikace Azure](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-deployment-update-settings.png)

7. V **publikování aplikaci Azure** dialogové okno, vyberte **Další** dokud **Souhrn** zobrazí se stránka. Ověřte nastavení a potom vyberte **publikovat**.
   
    ![Publikování na stránce Souhrn aplikace Azure](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-summary.png)

## <a name="next-steps"></a>Další postup
- [Pomocí sady Visual Studio aplikaci Azure Průvodce publikováním](vs-azure-tools-publish-azure-application-wizard.md)


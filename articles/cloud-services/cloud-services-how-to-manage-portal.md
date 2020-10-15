---
title: Běžné úkoly správy cloudových služeb | Microsoft Docs
description: Naučte se spravovat Cloud Services v Azure Portal. V těchto příkladech se používá Azure Portal.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: tagore
ms.openlocfilehash: 84e450a55f9482c3a713943adfcc4d7940ab873f
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92076704"
---
# <a name="manage-cloud-services-in-the-azure-portal"></a>Správa Cloud Services v Azure Portal
V **Cloud Services** oblasti Azure Portal můžete:

* Aktualizace role služby nebo nasazení.
* Zvyšte úroveň dvoufázového nasazení na produkční.
* Propojte prostředky s vaší cloudovou službou, abyste viděli závislosti prostředků a mohli škálovat prostředky dohromady.
* Odstraní cloudovou službu nebo nasazení.

Další informace o tom, jak škálovat cloudovou službu, najdete v tématu [Konfigurace automatického škálování pro cloudovou službu na portálu](cloud-services-how-to-scale-portal.md).

## <a name="update-a-cloud-service-role-or-deployment"></a>Aktualizace role nebo nasazení cloudové služby
Pokud potřebujete aktualizovat kód aplikace pro cloudovou službu, použijte příkaz **aktualizovat** v okně cloudová služba. Můžete aktualizovat jednu roli nebo všechny role. K aktualizaci můžete nahrát nový balíček služby nebo konfigurační soubor služby.

1. V [Azure Portal][Azure portal]Vyberte cloudovou službu, kterou chcete aktualizovat. Tento krok otevře okno instance cloudové služby.

2. V okně vyberte **aktualizovat**.

    ![Tlačítko Aktualizovat](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Aktualizujte nasazení pomocí nového souboru balíčku služby (. cspkg) a konfiguračního souboru služby (. cscfg).

    ![UpdateDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. Volitelně můžete aktualizovat účet úložiště a popisek nasazení.

5. Pokud má kterákoli role jenom jednu instanci role, vyberte **nasadit i v případě, že jedna nebo více rolí obsahuje zaškrtávací políčko jedna instance** , aby bylo možné pokračovat v upgradu.

    Azure může během aktualizace cloudové služby zaručit jenom 99,95% dostupnost služby, pokud má každá role aspoň dvě instance role (virtuální počítače). U dvou instancí role zpracovává jeden virtuální počítač požadavky klienta, zatímco druhý je aktualizovaný.

6. Zaškrtněte políčko **Spustit nasazení** , pokud chcete aktualizaci použít po dokončení nahrávání balíčku.

7. Vyberte **OK** a začněte aktualizovat službu.

## <a name="swap-deployments-to-promote-a-staged-deployment-to-production"></a>Prohození nasazení za účelem zvýšení úrovně dvoufázového nasazení na produkční
Pokud se rozhodnete nasadit novou verzi cloudové služby, fáze a otestuje novou verzi v přípravném prostředí cloudové služby. Pomocí **swapu** můžete přepínat mezi adresami URL, na které se obě nasazení řeší, a propagovat novou verzi do produkčního prostředí.

Nasazení můžete měnit na stránce **Cloud Services** nebo na řídicím panelu.

1. V [Azure Portal][Azure portal]Vyberte cloudovou službu, kterou chcete aktualizovat. Tento krok otevře okno instance cloudové služby.

2. V okně vyberte **swap**.

    ![Cloud Services – tlačítko pro přepnutí](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. Otevře se následující výzva k potvrzení:

    ![Cloud Services swap](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Po ověření informací o nasazení vyberte **OK** a Proměňte nasazení.

    Výměna nasazení probíhá rychle, protože jediná věc, kterou mění, jsou virtuální IP adresy (VIP) pro nasazení.

    Chcete-li ušetřit náklady na výpočetní výkon, můžete odstranit pracovní nasazení po ověření, že provozní nasazení funguje podle očekávání.

### <a name="common-questions-about-swapping-deployments"></a>Běžné dotazy týkající se odkládacích nasazení

**Jaké jsou požadavky na swapy nasazení?**

Úspěšná výměna nasazení má dva klíčové předpoklady:

- Pokud chcete pro produkční slot použít statickou IP adresu, musíte rezervovat i pro svůj přípravný slot. V opačném případě se výměna nezdařila.

- Než budete moct provést prohození, musí být spuštěné všechny instance vašich rolí. Stav vašich instancí můžete zjistit v okně **přehled** Azure Portal. Případně můžete použít příkaz [Get-AzureRole](/powershell/module/servicemanagement/azure.service/get-azurerole?view=azuresmps-3.7.0) ve Windows PowerShellu.

Upozorňujeme, že aktualizace operačního systému hosta a operace Service retušova taky můžou způsobit, že se swapy nasazení nezdaří. Další informace najdete v tématu [řešení problémů s nasazením cloudové služby](cloud-services-troubleshoot-deployment-problems.md).

**Účtuje se u své aplikace výpadky? Jak ho mám pokládat?**

Jak je popsáno v předchozí části, prohození nasazení je obvykle rychlé, protože se jedná o změnu konfigurace v nástroji pro vyrovnávání zatížení Azure. V některých případech může trvat 10 nebo více sekund a způsobit selhání při přechodném připojení. Pokud chcete omezit dopad na vaše zákazníky, zvažte implementaci [logiky opakování klienta](/azure/architecture/best-practices/transient-faults).

## <a name="delete-deployments-and-a-cloud-service"></a>Odstranit nasazení a cloudovou službu
Než budete moct odstranit cloudovou službu, musíte odstranit všechna existující nasazení.

Chcete-li ušetřit náklady na výpočetní výkon, můžete odstranit pracovní nasazení po ověření, že provozní nasazení funguje podle očekávání. Účtují se vám poplatky za výpočetní výkon pro nasazené instance rolí, které se zastavily.

Pomocí následujícího postupu můžete odstranit nasazení nebo cloudovou službu.

1. V [Azure Portal][Azure portal]Vyberte cloudovou službu, kterou chcete odstranit. Tento krok otevře okno instance cloudové služby.

2. V okně vyberte **Odstranit**.

    ![Tlačítko Odstranit Cloud Services](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. Pokud chcete odstranit celou cloudovou službu, zaškrtněte políčko **cloudová služba a její nasazení** . Nebo můžete vybrat buď **produkční nasazení** , nebo pracovní okno **nasazení** .

    ![Odstranit Cloud Services](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. V dolní části vyberte **Odstranit** .

5. Cloudovou službu odstraníte tak, že vyberete **Odstranit cloudovou službu**. Pak na výzvu k potvrzení vyberte **Ano**.

> [!NOTE]
> Po odstranění cloudové služby a konfiguraci podrobného monitorování musíte data z účtu úložiště odstranit ručně. Informace o tom, kde najít tabulky metrik, najdete v tématu [Úvod do monitorování cloudové služby](cloud-services-how-to-monitor.md).


## <a name="find-more-information-about-failed-deployments"></a>Najít další informace o neúspěšném nasazení
Okno **Přehled** má v horní části stavový řádek. Když vyberete pruh, otevře se nové okno a zobrazí se všechny informace o chybě. Pokud nasazení neobsahuje žádné chyby, okno informace je prázdné.

![Přehled Cloud Services](./media/cloud-services-how-to-manage-portal/status-info.png)



[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>Další kroky
* [Obecná konfigurace cloudové služby](cloud-services-how-to-configure-portal.md)
* Přečtěte si, jak [nasadit cloudovou službu](cloud-services-how-to-create-deploy-portal.md).
* Nakonfigurujte [vlastní název domény](cloud-services-custom-domain-name-portal.md).
* Nakonfigurujte [certifikáty TLS/SSL](cloud-services-configure-ssl-certificate-portal.md).
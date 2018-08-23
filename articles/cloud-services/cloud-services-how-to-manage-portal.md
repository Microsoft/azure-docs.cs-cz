---
title: Běžné úkoly správy cloudových služeb | Dokumentace Microsoftu
description: Zjistěte, jak spravovat Cloud Services na webu Azure Portal. Tyto příklady pomocí webu Azure portal.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: cb218ad9-77d4-4149-83db-71159c00767e
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeconnoc
ms.openlocfilehash: e9f4153c68f0a2a4ce83f900ff63152311163ff6
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2018
ms.locfileid: "42055229"
---
# <a name="manage-cloud-services-in-the-azure-portal"></a>Správa cloudové služby na webu Azure Portal
V **Cloud Services** oblast na webu Azure portal, můžete:

* Aktualizujte roli služby nebo nasazení.
* Umožňuje zvýšit úroveň dvoufázového nasazení do produkčního prostředí.
* Propojení se prostředky ke cloudové službě, mohli zobrazit závislosti prostředků a společně škálovat prostředky.
* Odstraňte cloudovou službu nebo nasazení.

Další informace o tom, jak škálovat cloudové služby, najdete v části [nakonfigurovat automatické škálování pro cloudovou službu na portálu](cloud-services-how-to-scale-portal.md).

## <a name="update-a-cloud-service-role-or-deployment"></a>Aktualizace role cloudové služby nebo nasazení
Pokud je potřeba aktualizovat kód aplikace pro cloudovou službu, použijte **aktualizovat** v okně cloudové služby. Můžete aktualizovat jedné role nebo všechny role. Pokud chcete aktualizovat, můžete nahrát nového balíčku nebo konfigurační soubor služby.

1. V [webu Azure portal][Azure portal], vyberte cloudovou službu, kterou chcete aktualizovat. Tento krok otevře okno instance cloudové služby.

2. V okně vyberte **aktualizace**.

    ![Tlačítko Aktualizovat](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Aktualizujte nasazení nového souboru balíku služeb (.cspkg) a konfigurační soubor služby (.cscfg).

    ![UpdateDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. Volitelně můžete Aktualizujte účet úložiště a označení nasazení.

5. Pokud mají všechny role pouze jedna instance role, vyberte **nasadit, i když jedna nebo víc rolí obsahuje jednu instanci** zaškrtnutím políčka Povolit upgrade, aby bylo možné pokračovat.

    Azure může zaručit pouze 99,95 % dostupnost služeb během aktualizace cloudové služby, pokud každá role má aspoň dvě instance role (virtuální počítače). S dvě instance role jeden virtuální počítač zpracovává požadavky na klienta během druhé je aktualizována.

6. Vyberte **spuštění nasazení** zaškrtávací políčko k použití aktualizace po dokončení nahrávání balíčku.

7. Vyberte **OK** zahájíte aktualizaci služby.

## <a name="swap-deployments-to-promote-a-staged-deployment-to-production"></a>Záměnu nasazení, když se zvýšit úroveň dvoufázového nasazení do produkčního prostředí
Až se rozhodnete nasadit novou verzi je Cloudová služba, fáze a otestovat novou verzi ve vašem cloudovém prostředí pracovní služby. Použití **Prohodit** přepnout adresy URL, podle kterých dvě nasazení se tak vyřeší a podporují novou verzi do produkčního prostředí.

Zaměnit nasazení z **Cloud Services** stránku nebo řídicí panel.

1. V [webu Azure portal][Azure portal], vyberte cloudovou službu, kterou chcete aktualizovat. Tento krok otevře okno instance cloudové služby.

2. V okně vyberte **Prohodit**.

    ![Cloud Services Prohodit tlačítko](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. Otevře se následující výzva k potvrzení:

    ![Cloud Services prohození](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Až ověříte informace o nasazení, vyberte **OK** do odkládacího souboru nasazení.

    Přepnutí nasazení dochází rychle virtuálních IP adres (VIP) je jediné, co se změní pro nasazení.

    Chcete-li uložit náklady na výpočetní výkon, můžete odstranit pracovní nasazení po ověření, že produkční nasazení funguje podle očekávání.

### <a name="common-questions-about-swapping-deployments"></a>Běžné dotazy týkající se nasazení prohození

**Jaké jsou požadavky pro prohození nasazení?**

Existují dva klíče požadavky pro prohození úspěšné nasazení:

- Pokud chcete používat statickou IP adresu pro vaše produkční slot, musíte rezervovat, jeden pro vaše přípravný slot. V opačném případě prohození nezdaří.

- Všechny instance role musí běžet, než bude možné provést prohození. Můžete zkontrolovat stav instancí vašich **přehled** okna na webu Azure portal. Alternativně můžete použít [Get-AzureRole](/powershell/module/servicemanagement/azure/get-azurerole?view=azuresmps-3.7.0) příkazu v prostředí Windows PowerShell.

Všimněte si, že aktualizace hostovaného operačního systému a také opravy operací služby může způsobit záměna nasazení selže. Další informace najdete v tématu [Poradce při potížích problémů s nasazením cloudové služby](cloud-services-troubleshoot-deployment-problems.md).

**Nesnižuje prohození výpadku pro mé aplikace? Jak je by ji zpracovat?**

Jak je popsáno v předchozí části, je obvykle rychlé přepnutí nasazení, protože je právě změnu konfigurace ve službě Azure load balancer. V některých případech může trvat 10 nebo další sekund a výsledek selhání přechodné připojení. Pokud chcete omezit dopad na vaše zákazníky, zvažte implementaci [logika opakovaných pokusů klienta](../best-practices-retry-general.md).

## <a name="delete-deployments-and-a-cloud-service"></a>Odstranit nasazení a cloudové služby
Než budete moct odstranit cloudovou službu, musíte odstranit každé existující nasazení.

Chcete-li uložit náklady na výpočetní výkon, můžete odstranit pracovní nasazení po ověření, že produkční nasazení funguje podle očekávání. Účtuje se vám výpočetní náklady pro instance nasazené rolí, které jsou zastaveny.

Pomocí následujícího postupu odstraníte nasazení aplikace nebo cloudové služby.

1. V [webu Azure portal][Azure portal], vyberte cloudovou službu, kterou chcete odstranit. Tento krok otevře okno instance cloudové služby.

2. V okně vyberte **odstranit**.

    ![Cloud Services odstranit tlačítko](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. Pokud chcete odstranit celou cloudové služby, vyberte **Cloudová služba a její nasazení** zaškrtávací políčko. Nebo můžete použít buď **produkční nasazení** nebo **pracovní nasazení** zaškrtávací políčko.

    ![Odstranění cloudové služby](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. Vyberte **odstranit** v dolní části.

5. Pokud chcete odstranit cloudovou službu, vyberte **odstranit cloudovou službu**. Vyberte na řádku potvrzení **Ano**.

> [!NOTE]
> Pokud cloudové služby se odstraní a podrobné monitorování je nastavena, musíte odstranit data ručně z vašeho účtu úložiště. Informace o tom, kde najít tabulky metrik najdete v tématu [Úvod do cloudové služby monitorování](cloud-services-how-to-monitor.md).


## <a name="find-more-information-about-failed-deployments"></a>Najít další informace o selhání nasazení
**Přehled** okno má stavový řádek v horní části. Když vyberete panelu, nové okno otevře a zobrazí všechny informace o chybě. Pokud nasazení neobsahuje nějaké chyby, v okně informace je prázdný.

![Přehled služby cloud Services](./media/cloud-services-how-to-manage-portal/status-info.png)



[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>Další postup
* [Obecná konfigurace cloudové služby](cloud-services-how-to-configure-portal.md).
* Zjistěte, jak [nasadit cloudovou službu](cloud-services-how-to-create-deploy-portal.md).
* Konfigurace [vlastního názvu domény](cloud-services-custom-domain-name-portal.md).
* Konfigurace [certifikáty SSL](cloud-services-configure-ssl-certificate-portal.md).

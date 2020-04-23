---
title: Adaptivní řízení aplikací v Azure Security Center
description: Tento dokument vám pomůže použít adaptivní řízení aplikací v Azure Security Center k přidávání aplikací spuštěných na počítačích Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/23/2019
ms.author: memildin
ms.openlocfilehash: 1dc94c5ec08cc27fb1819ccc16fd766c62aad796
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604676"
---
# <a name="adaptive-application-controls"></a>Adaptivní řízení aplikací
Pomocí tohoto názorného postupu zjistíte, jak nakonfigurovat řízení aplikací v Azure Security Center.

## <a name="what-are-adaptive-application-controls-in-security-center"></a>Co je adaptivní řízení aplikací ve službě Security Center?
Adaptivní řízení aplikací je inteligentní, automatizované a ucelené řešení z Azure Security Center, které vám pomůže určit, které aplikace se můžou spouštět na počítačích Azure a mimo Azure (Windows a Linux). Kromě dalších výhod to pomáhá posílit zabezpečení vašich počítačů proti malwaru. Security Center využívá strojové učení k analýze aplikací spuštěných na vašich počítačích a k vytvoření seznamu povolených z těchto inteligentních funkcí. Tato funkce značně zjednodušuje proces konfigurace a správy zásad seznamu povolených aplikací, což vám umožní:

- Blokuje nebo upozorňuje na pokusy o spuštění škodlivých aplikací, včetně těch, které by jinak mohly chybět antimalwarová řešení.
- Dodržovat zásady zabezpečení vaší organizace, které vyžadují používání pouze licencovaného softwaru.
- Zamezit používání nežádoucího softwaru ve vašem prostředí.
- Zamezit spouštění starých a nepodporovaných aplikací.
- Zabránit používání konkrétních softwarových nástrojů, které nejsou ve vaší organizaci povolené.
- Umožnit IT oddělení řídit přístup k citlivým datům prostřednictvím používání aplikace.

> [!NOTE]
> Pro počítače, které nejsou v Azure a Linux, jsou adaptivní řízení aplikací podporované jenom v režimu auditování.

## <a name="how-to-enable-adaptive-application-controls"></a>Jak povolit adaptivní řízení aplikací?

Adaptivní řízení aplikací vám umožní definovat sadu aplikací, které můžou běžet na konfigurovaných skupinách počítačů. Tato funkce je k dispozici pro Azure i mimo Azure Windows (všechny verze, Classic nebo Azure Resource Manager) a počítače se systémem Linux. Pomocí následujících kroků můžete nakonfigurovat seznamy povolených aplikací:

1. Otevřete řídicí panel **Security Center**.

1. V levém podokně vyberte **Adaptivní řízení aplikací** umístěné v části **Pokročilá obrana cloudu**.

    [![Obrana](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png)](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png#lightbox)

Zobrazí se stránka **Adaptivní řízení aplikací**.

![ovládací prvky](./media/security-center-adaptive-application/security-center-adaptive-application-fig2.png)

Část **Skupiny virtuálních počítačů** obsahuje tři karty:

* **Nakonfigurované:** Seznam skupin obsahujících virtuální počítače s nakonfigurovaným řízením aplikací.
* **Doporučené:** Seznam skupin, pro které se doporučuje řízení aplikací. Služba Security Center pomocí strojového učení identifikuje virtuální počítače, které jsou vhodnými kandidáty pro řízení aplikací, na základě toho, jestli virtuální počítače spouštějí stále stejné aplikace.
* **Žádné doporučení:** Seznam skupin obsahujících virtuální počítače bez jakýchkoli doporučení pro řízení aplikací. Například virtuální počítače, na kterých se neustále mění aplikace a které se ještě nedostaly do stabilního stavu.

> [!NOTE]
> Služba Security Center pomocí vlastního algoritmu clusteringu vytváří skupiny virtuálních počítačů a zajišťuje, že podobné virtuální počítače získají optimální doporučenou zásadu řízení aplikací.
>
>

### <a name="configure-a-new-application-control-policy"></a>Konfigurace nové zásady řízení aplikací

1. Vyberte kartu **Doporučené** pro seznam skupin s doporučeními pro řízení aplikací:

   ![Doporučené](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

   Seznam obsahuje:

   - **Název skupiny**: název předplatného a skupiny.
   - **Virtuální počítače a počítače**: počet virtuálních počítačů ve skupině
   - **Stav**: stav doporučení
   - **Závažnost**: úroveň závažnosti doporučení

2. Kliknutím na skupinu otevřete možnost **vytvořit pravidla řízení aplikací** .

   [![Pravidla řízení aplikací](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png#lightbox)

3. V části **Vybrat virtuální počítače**zkontrolujte seznam doporučených virtuálních počítačů a zrušte zaškrtnutí všech nechcete použít zásadu pro přidávání aplikací do aplikace. Dále se zobrazí dva seznamy:

   - **Doporučené aplikace**: seznam aplikací, které jsou na virtuálních počítačích v této skupině časté a které se doporučuje spouštět.
   - **Další aplikace**: seznam aplikací, které jsou buď méně časté na virtuálních počítačích v této skupině, nebo které se označují jako zneužitelné (Další informace najdete níže) a doporučené pro kontrolu.

4. Zkontrolujte aplikace v obou seznamech a zrušte zaškrtnutí u těch, které nechcete použít. Oba seznamy obsahují:

   - **Název**: informace o certifikátu nebo úplná cesta k aplikaci
   - **TYPY SOUBORŮ:** Typ souboru aplikace. Může to být EXE, skript, MSI nebo jakékoli permutace těchto typů.
   - **Zneužitelné**: ikona upozornění značí, jestli by útočník mohl použít určitou aplikaci k obejít seznamu povolených aplikací. Tyto aplikace doporučujeme před schválením zkontrolovat.
   - **UŽIVATELÉ:** Uživatelé, kterým se doporučuje povolit spuštění aplikace.

5. Jakmile budete s výběry hotovi, vyberte **Vytvořit**. <br>
   Po vybrání vytvořit Azure Security Center automaticky vytvoří vhodná pravidla na základě integrovaného řešení seznamu povolených aplikací, které je dostupné na Windows serverech (AppLocker).

> [!NOTE]
> - Služba Security Center potřebuje minimálně dva týdny dat, aby mohla vytvořit základní hodnoty a doplnit jedinečná doporučení pro skupinu virtuálních počítačů. Noví zákazníci služby Security Center úrovně Standard by měli počítat s chováním, kdy se skupiny virtuálních počítačů zpočátku zobrazí na kartě *Žádné doporučení*.
> - Adaptivní řízení aplikací služby Security Center nepodporuje virtuální počítače, které nemají zapnuté zásady AppLockeru objektem zásad skupiny nebo místními zásadami zabezpečení.
> -  Z hlediska zabezpečení se Security Center vždy pokusí vytvořit pravidlo vydavatele pro aplikace, které jsou vybrány jako povolené, a pouze v případě, že aplikace nemá informace o vydavateli (označuje se jako nepodepsaná), vytvoří se pravidlo cesty pro úplnou cestu konkrétní aplikace.
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>Úpravy a monitorování skupiny s nakonfigurovaným řízením aplikací

1. Pokud chcete upravit a monitorovat skupinu s nakonfigurovanou zásadou seznamu povolených aplikací, vraťte se na stránku **Adaptivní řízení aplikací** a vyberte **nakonfigurovaná** v rámci **skupin virtuálních počítačů**:

   ![Skupiny](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

   Seznam obsahuje:

   - **Název skupiny**: název předplatného a skupiny.
   - **Virtuální počítače a počítače**: počet virtuálních počítačů ve skupině
   - **Režim**: režim auditu se pokusí spustit aplikace, které nejsou na seznamu povolených. Vynutilit neumožní spouštění aplikací, pokud nejsou na seznamu povolených.
   - **Výstrahy**: všechna aktuální porušení

2. Kliknutím na skupinu provedete změny na stránce **Upravit zásadu řízení aplikací** .

   ![Ochrana](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

3. V části **Režim ochrany** máte na výběr mezi následujícími možnostmi:

   - **Audit:** V tomto režimu řešení řízení aplikací nevynucuje pravidla a audituje jenom aktivitu na chráněných virtuálních počítačích. Tato možnost se doporučuje pro scénáře, kdy chcete nejprve sledovat celkové chování před tím, než zablokujete spouštění aplikace na cílovém virtuálním počítači.
   - **Vynucení:** V tomto režimu řešení řízení aplikací vynucuje pravidla a zajišťuje zablokování aplikací, které nemají povolené spouštění.

   > [!NOTE]
   > -  **Vyvynucování** režimu ochrany je zakázáno až do dalšího upozornění.
   > - Jak už jsme zmínili, ve výchozím nastavení se nová zásada řízení aplikací vždy nakonfiguruje v režimu *Audit*. 
   >

4. V části **rozšíření zásad**přidejte libovolnou cestu aplikace, kterou chcete použít. Po přidání těchto cest Security Center aktualizuje zásadu seznam povolených aplikací na virtuálních počítačích v rámci vybrané skupiny virtuálních počítačů a pro tyto aplikace vytvoří vhodná pravidla, a to spolu s pravidly, která už jsou v platnosti.

5. Zkontrolujte aktuální porušení uvedená v části **nedávné výstrahy** . Klikněte na každý řádek, který se přesměruje na stránku **výstrahy** v rámci Azure Security Center a zobrazí všechny výstrahy zjištěné Azure Security Center na přidružených virtuálních počítačích.
   - **Výstrahy**: všechna porušení, která byla zaznamenána.
   - **Ne. Virtuálních**počítačů: počet virtuálních počítačů s tímto typem výstrahy.

6. V části **Pravidla přidávání vydavatelů**, pravidla přidávání na seznam **povolených**a pravidla přidávání do seznamu povolených **hodnot hash** můžete zobrazit, která pravidla povolená pro aplikace jsou aktuálně nakonfigurovaná na virtuálních počítačích v rámci skupiny podle typu kolekce pravidel. Pro každé pravidlo můžete zobrazit:

   - **Pravidlo**: konkrétní parametry, podle kterých je aplikace prozkoumána nástrojem AppLocker, aby bylo možné zjistit, zda je povoleno spuštění aplikace.
   - **Typ souboru**: typy souborů, na které se vztahuje konkrétní pravidlo. Může to být kterýkoli z následujících příkazů: EXE, Script, MSI nebo jakékoli permutace těchto typů souborů.
   - **Uživatelé**: jméno nebo počet uživatelů, kteří mají povoleno spuštění aplikace, která je pokrytá pravidlem pro seznam povolených aplikací.

   ![Pravidla přidávání na seznam povolených](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

7. Pokud chcete odstranit konkrétní pravidlo nebo upravit povolené uživatele, klikněte na tři tečky na konci každého řádku.

8. Po provedení změn v zásadě **Adaptivní řízení aplikací** klikněte na **Uložit**.

### <a name="not-recommended-list"></a>Seznam nedoporučených

Security Center jenom pro virtuální počítače, na kterých běží stabilní sada aplikací, doporučuje zásady přidávání aplikací do seznamu. Pokud se aplikace na přidružených virtuálních počítačích neustále mění, doporučení se nevytvoří.

![Doporučení](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

Seznam obsahuje:
- **Název skupiny**: název předplatného a skupiny.
- **Virtuální počítače a počítače**: počet virtuálních počítačů ve skupině

Azure Security Center umožňuje definovat taky zásady přidávání aplikací na seznam povolených pro nedoporučené skupiny virtuálních počítačů. Řiďte se stejnými zásadami, jak jsme už dříve popsali, ke konfiguraci zásad seznamu povolených aplikací u těchto skupin taky.

## <a name="move-a-vm-from-one-group-to-another"></a>Přesunutí virtuálního počítače z jedné skupiny do druhé

 Když přesunete virtuální počítač z jedné skupiny do druhé, uplatní se u něj zásada řízení aplikací na nastavení skupiny, do které jste ji přesunuli. Virtuální počítač můžete také přesunout z nakonfigurované skupiny do nenakonfigurované skupiny, což vede k odebrání všech zásad řízení aplikací, které byly dříve aplikovány na virtuální počítač.

 1. Na stránce **Adaptivní řízení aplikací** klikněte na kartě **konfigurovaná** na skupinu, do které virtuální počítač, který se má aktuálně přesunout, patří.
1. Klikněte na možnost **nakonfigurované virtuální počítače a počítače**.
1. Klikněte na tři tečky na řádku virtuálního počítače, které chcete přesunout, a klikněte na **přesunout**. Otevře se okno **přesunout počítač do jiné skupiny** .

    ![Ochrana](./media/security-center-adaptive-application/adaptive-application-move-group.png)

 1. Vyberte skupinu, do které chcete virtuální počítač přesunout, a klikněte na **přesunout počítač**a pak klikněte na **Uložit**.

    ![Ochrana](./media/security-center-adaptive-application/adaptive-application-move-group2.png)

 > [!NOTE]
> Po kliknutí na **přesunout počítač**nezapomeňte kliknout na **Uložit** . Pokud nekliknete na **Uložit**, počítač nebude přesunut.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak používat Adaptivní řízení aplikací v Azure Security Center k přidávání aplikací spuštěných na virtuálních počítačích Azure a mimo Azure. Pokud se o službě Azure Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Správa a zpracování výstrah zabezpečení ve službě Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Zjistěte, jak spravovat výstrahy a reagovat na incidenty zabezpečení ve službě Security Center.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md). Zjistěte, jak monitorovat stav svých prostředků Azure.
* [Principy výstrah zabezpečení v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Seznamte se s dalšími typy výstrah zabezpečení.
* [Průvodce odstraňováním potíží pro službu Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Zjistěte, jak řešit běžné problémy ve službě Security Center.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/). Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.

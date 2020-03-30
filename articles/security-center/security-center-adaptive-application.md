---
title: Adaptivní řízení aplikací v Azure Security Center
description: Tento dokument vám pomůže používat adaptivní řízení aplikací v Azure Security Center na whitelist aplikace spuštěné v počítačích Azure.
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
Adaptivní řízení aplikací je inteligentní, automatizované komplexní řešení z Azure Security Center, které vám pomůže řídit, které aplikace můžou běžet na vašich počítačích Azure i mimo Azure (Windows a Linux). Mezi další výhody, To pomáhá posílit vaše stroje proti malwaru. Security Center používá strojové učení k analýze aplikací spuštěné na vašich počítačích a vytváří seznam povolených položek z této inteligence. Tato funkce výrazně zjednodušuje proces konfigurace a údržby zásad seznamu povolených aplikací, což umožňuje:

- Blokovat nebo upozornit na pokusy o spuštění škodlivých aplikací, včetně těch, které by jinak mohly být vynechány antimalwarovými řešeními.
- Dodržovat zásady zabezpečení vaší organizace, které vyžadují používání pouze licencovaného softwaru.
- Zamezit používání nežádoucího softwaru ve vašem prostředí.
- Zamezit spouštění starých a nepodporovaných aplikací.
- Zabránit používání konkrétních softwarových nástrojů, které nejsou ve vaší organizaci povolené.
- Umožnit IT oddělení řídit přístup k citlivým datům prostřednictvím používání aplikace.

> [!NOTE]
> Pro počítače mimo Azure a Linux adaptivní ovládací prvky aplikací jsou podporovány pouze v režimu auditování.

## <a name="how-to-enable-adaptive-application-controls"></a>Jak povolit adaptivní řízení aplikací?

Adaptivní ovládací prvky aplikací pomáhají definovat sadu aplikací, které mohou být spuštěny na konfigurovaných skupinách počítačů. Tato funkce je dostupná pro azure i neazure windows (všechny verze, klasické nebo Azure Resource Manager) a linuxové počítače. Ke konfiguraci seznamů povolení aplikace použijte následující kroky:

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

1. Vyberte kartu **Doporučeno** pro seznam skupin s doporučeními pro řízení aplikací:

   ![Doporučené](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

   Seznam obsahuje:

   - **Název skupiny**: Název předplatného a skupiny
   - **Virtuální počítače a počítače**: Počet virtuálních počítačů ve skupině
   - **Stát**: stav doporučení
   - **Závažnost**: úroveň závažnosti doporučení

2. Kliknutím na skupinu otevřete možnost **Vytvořit pravidla řízení aplikace.**

   [![Pravidla řízení aplikací](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png#lightbox)

3. V **seznamu Vybrat virtuální uživatele**zkontrolujte seznam doporučených virtuálních aplikací a zrušte zaškrtnutí všech, na které nechcete použít zásady seznamu povolených aplikací. Dále se zobrazí dva seznamy:

   - **Doporučené aplikace**: seznam aplikací, které jsou časté na virtuálních počítačích v rámci této skupiny a doporučuje se, aby bylo povoleno spouštět.
   - **Další aplikace**: seznam aplikací, které jsou buď méně časté na virtuálních počítačích v rámci této skupiny, nebo které jsou známé jako Exploitables (viz další níže) a doporučené pro kontrolu.

4. Zkontrolujte aplikace v obou seznamech a zrušte zaškrtnutí u těch, které nechcete použít. Oba seznamy obsahují:

   - **NÁZEV**: informace o certifikátu nebo úplná cesta k žádosti
   - **TYPY SOUBORŮ:** Typ souboru aplikace. To může být EXE, Skript, MSI nebo permutace těchto typů.
   - **ZNEUŽITELNÉ**: ikona upozornění označuje, zda by útočník mohl použít určitou aplikaci k obejití seznamu povolených aplikací. Tyto aplikace doporučujeme před schválením zkontrolovat.
   - **UŽIVATELÉ:** Uživatelé, kterým se doporučuje povolit spuštění aplikace.

5. Jakmile budete s výběry hotovi, vyberte **Vytvořit**. <br>
   Po výběru vytvořit Azure Security Center automaticky vytvoří příslušná pravidla nad integrované řešení seznamu povolených aplikací, které je k dispozici na serverech windows (AppLocker).

> [!NOTE]
> - Služba Security Center potřebuje minimálně dva týdny dat, aby mohla vytvořit základní hodnoty a doplnit jedinečná doporučení pro skupinu virtuálních počítačů. Noví zákazníci služby Security Center úrovně Standard by měli počítat s chováním, kdy se skupiny virtuálních počítačů zpočátku zobrazí na kartě *Žádné doporučení*.
> - Adaptivní řízení aplikací služby Security Center nepodporuje virtuální počítače, které nemají zapnuté zásady AppLockeru objektem zásad skupiny nebo místními zásadami zabezpečení.
> -  Z bezpečnostních důvodů se Centrum zabezpečení vždy pokusí vytvořit pravidlo vydavatele pro aplikace, které jsou vybrány jako povolené, a pouze v případě, že aplikace nemá informace o vydavateli (aka není podepsáno), bude vytvořeno pravidlo cesty pro úplnou cestu konkrétní aplikaci.
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>Úpravy a monitorování skupiny s nakonfigurovaným řízením aplikací

1. Chcete-li upravit a sledovat skupinu nakonfigurovanou pomocí zásad seznamu povolených aplikací, vraťte se na stránku **Adaptivní ovládací prvky aplikací** a vyberte možnost **NAKONFIGUROVÁNO** v části **Skupiny virtuálních počítače**:

   ![Skupiny](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

   Seznam obsahuje:

   - **Název skupiny**: název předplatného a skupiny
   - **Virtuální počítače a počítače**: počet virtuálních počítačů ve skupině
   - **Režim**: Režim auditování bude protokolovat pokusy o spuštění aplikací, které nejsou v seznamu povolených položek. Vynucení nepovolí spuštění aplikací, pokud nejsou na seznamu povolených položek.
   - **Upozornění**: jakékoli aktuální porušení

2. Kliknutím na skupinu můžete provést změny na stránce **Upravit zásady řízení aplikace.**

   ![Ochrana](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

3. V části **Režim ochrany** máte na výběr mezi následujícími možnostmi:

   - **Audit:** V tomto režimu řešení řízení aplikací nevynucuje pravidla a audituje jenom aktivitu na chráněných virtuálních počítačích. Tato možnost se doporučuje pro scénáře, kdy chcete nejprve sledovat celkové chování před tím, než zablokujete spouštění aplikace na cílovém virtuálním počítači.
   - **Vynucení:** V tomto režimu řešení řízení aplikací vynucuje pravidla a zajišťuje zablokování aplikací, které nemají povolené spouštění.

   > [!NOTE]
   > -  **Režim vynucení** ochrany je až do odvolání zakázán.
   > - Jak už jsme zmínili, ve výchozím nastavení se nová zásada řízení aplikací vždy nakonfiguruje v režimu *Audit*. 
   >

4. V části **Rozšíření zásad**přidejte libovolnou cestu aplikace, kterou chcete povolit. Po přidání těchto cest Centrum zabezpečení aktualizuje zásady seznamu povolených aplikací na virtuálních počítačích v rámci vybrané skupiny vsystému v rámci služby VMS a vytvoří příslušná pravidla pro tyto aplikace, kromě pravidel, která jsou již na místě.

5. Zkontrolujte aktuální porušení uvedená v části **Poslední výstrahy.** Kliknutím na každý řádek přesměrujte na stránku **Výstrahy** v Centru zabezpečení Azure a zobrazte všechny výstrahy, které zjistilo Centrum zabezpečení Azure na přidružených virtuálních počítačích.
   - **Upozornění**: všechna porušení, která byla zaznamenána.
   - **Ne. virtuálních počítačů**: počet virtuálních počítačů s tímto typem výstrahy.

6. V části **Whitelisting rules aplikace ,** **Path whitelisting rules**a **Hash whitelisting rules** you see which application whitelisting rules are currently configured on the VMs within a group, podle typu kolekce pravidel. U každého pravidla vidíte:

   - **Pravidlo**: Konkrétní parametry, podle kterých je aplikace zkontrolována nástrojem AppLocker, aby zjistila, zda je povoleno spuštění aplikace.
   - **Typ souboru**: Typy souborů, na které se vztahuje určité pravidlo. Může se jednalo o některou z následujících možností: EXE, Script, MSI nebo jakákoli permutace těchto typů souborů.
   - **Uživatelé**: Název nebo počet uživatelů, kteří mohou spouštět aplikaci, na kterou se vztahuje pravidlo seznamu povolených aplikací.

   ![Pravidla přidávání na seznam povolených](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

7. Pokud chcete odstranit konkrétní pravidlo nebo upravit povolené uživatele, klikněte na tři tečky na konci každého řádku.

8. Po provedení změn v zásadách **adaptivních ovládacích prvků aplikace** klepněte na tlačítko **Uložit**.

### <a name="not-recommended-list"></a>Seznam nedoporučených

Security Center doporučuje pouze zásady whitelisting aplikace pro virtuální počítače se stabilní sadou aplikací. Pokud se aplikace na přidružených virtuálních počítačích neustále mění, doporučení se nevytvoří.

![Doporučení](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

Seznam obsahuje:
- **Název skupiny**: název předplatného a skupiny
- **Virtuální počítače a počítače**: počet virtuálních počítačů ve skupině

Azure Security Center umožňuje definovat zásady whitelisting aplikace na nedoporučené skupiny virtuálních počítačích také. Postupujte podle stejných zásad, jak bylo popsáno dříve, chcete-li nakonfigurovat zásady whitelisting aplikace na tyto skupiny také.

## <a name="move-a-vm-from-one-group-to-another"></a>Přesunutí virtuálního virtuálního uživatele z jedné skupiny do druhé

 Když přesunete virtuální ho z jedné skupiny do druhé, zásady řízení aplikací, které se na něj použijí, se změní na nastavení skupiny, do které jste ho přesunuli. Virtuální hotel můžete také přesunout z nakonfigurované skupiny do nenakonfigurované skupiny, což vede k odebrání všech zásad řízení aplikací, které byly dříve použity pro virtuální počítače.

 1. Na stránce **Adaptivní ovládací prvky aplikace** klikněte na kartě **CONFIGURED** na skupinu, do které virtuální modul, do kterého má být aktuálně přesunut, patří.
1. Klepněte na **položku Nakonfigurované virtuální počítače a počítače**.
1. Kliknutím na tři tečky v řádku virtuálního virtuálního virtuálního virtuálního souboru přesuňte a klikněte na **Přesunout**. Otevře **se okno Přesunout počítač do jiné skupiny.**

    ![Ochrana](./media/security-center-adaptive-application/adaptive-application-move-group.png)

 1. Vyberte skupinu, do které chcete virtuální počítač přesunout, klikněte na **Přesunout počítač**a klepněte na **uložit**.

    ![Ochrana](./media/security-center-adaptive-application/adaptive-application-move-group2.png)

 > [!NOTE]
> Po klepnutí na tlačítko **Přesunout počítač**nezapomeňte klepnout na tlačítko **Uložit** . Pokud neklepnete na tlačítko **Uložit**, počítač nebude přesunut.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se naučili používat adaptivní řízení aplikací v Azure Security Center na seznam aplikací spuštěných v Azure a mimo azure virtuálnípočítače. Pokud se o službě Azure Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Správa a zpracování výstrah zabezpečení ve službě Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Zjistěte, jak spravovat výstrahy a reagovat na incidenty zabezpečení ve službě Security Center.
* [Monitorování stavu zabezpečení v Azure Security Center](security-center-monitoring.md). Zjistěte, jak monitorovat stav svých prostředků Azure.
* [Principy výstrah zabezpečení v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Seznamte se s dalšími typy výstrah zabezpečení.
* [Průvodce odstraňováním potíží pro službu Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Zjistěte, jak řešit běžné problémy ve službě Security Center.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/). Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.

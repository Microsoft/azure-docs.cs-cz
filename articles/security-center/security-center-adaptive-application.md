---
title: Adaptivní řízení aplikací v Azure Security Center | Microsoft Docs
description: Tento dokument vám pomůže použít adaptivní řízení aplikací v Azure Security Center k přidávání aplikací spuštěných na virtuálních počítačích Azure na seznam povolených.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: rkarlin
ms.openlocfilehash: ddf9c5e30a27a829a74ccf0985dce30a68f9bbb7
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51256642"
---
# <a name="adaptive-application-controls-in-azure-security-center"></a>Adaptivní řízení aplikací v Azure Security Center
Pomocí tohoto názorného postupu zjistíte, jak nakonfigurovat řízení aplikací v Azure Security Center.

## <a name="what-are-adaptive-application-controls-in-security-center"></a>Co je adaptivní řízení aplikací ve službě Security Center?
Adaptivní řízení aplikací je přidání na seznam povolených řešení inteligentní, automatizované začátku do konce aplikace ze služby Azure Security Center. To pomáhá řídit, které aplikace se můžou spouštět na vašich virtuálních počítačích v Azure, což kromě dalších výhod posiluje ochranu virtuálních počítačů před malwarem. Security Center pomocí strojového učení analyzuje aplikace spuštěné na virtuálních počítačích a pomáhá konkrétní aplikováním pravidel těchto informací. Tato funkce výrazně zjednodušuje proces konfigurace a Správa zásad na seznam povolených aplikací, umožňuje:

- Blokovat nebo upozorňovat na pokusy o spuštění škodlivých aplikací, včetně těch, které může být jinak mohla vynechat antimalwarová řešení.
- Dodržovat zásady zabezpečení vaší organizace, které vyžadují používání pouze licencovaného softwaru.
- Zamezit používání nežádoucího softwaru ve vašem prostředí.
- Zamezit spouštění starých a nepodporovaných aplikací.
- Zabránit používání konkrétních softwarových nástrojů, které nejsou ve vaší organizaci povolené.
- Umožnit IT oddělení řídit přístup k citlivým datům prostřednictvím používání aplikace.

## <a name="how-to-enable-adaptive-application-controls"></a>Jak povolit adaptivní řízení aplikací?
Adaptivní řízení aplikací pomáhá definovat sadu aplikací, které jsou povoleno spouštění v nakonfigurovaných skupinách virtuálních počítačů. Tato funkce je dostupná pouze pro počítače s Windows (všechny verze, Classic nebo Azure Resource Manager). Pokud chcete ve službě Security Center nakonfigurovat přidávání aplikací na seznam povolených, můžete postupovat podle následujících kroků:

1. Otevřete řídicí panel **Security Center**.
2. V levém podokně vyberte **Adaptivní řízení aplikací** umístěné v části **Pokročilá obrana cloudu**.

    ![Obrana](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png)

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
1. Kliknutím na kartu **Doporučené** zobrazte seznam skupin s doporučenými pro řízení aplikací:

  ![Doporučené](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

  Seznam obsahuje:

  - **NÁZEV:** Název předplatného a skupiny.
  - **VIRTUÁLNÍ POČÍTAČE:** Počet virtuálních počítačů ve skupině.
  - **Stav**: stav doporučení
  - **ZÁVAŽNOST:** Úroveň závažnosti doporučení.

2. Klikněte na skupinu, která otevře **vytvořit pravidla řízení aplikací** možnost.

  ![Pravidla řízení aplikací](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)

3. V **vybrat virtuální počítače**zkontrolujte seznam doporučených virtuálních počítačů a zrušte zaškrtnutí těch, nechcete použít zásady whitelising aplikace k. Dále se zobrazí dva seznamy:

  - **Doporučené aplikace**: seznam aplikací, které se často vyskytují na virtuálních počítačích v této skupině a doporučený postup bude moct spustit.
  - **Další aplikace**: seznam aplikací, které jsou méně často na virtuálních počítačích v této skupině nebo, který se označuje jako zneužitelné (Další informace najdete níže) a doporučuje zkontrolovat.

4. Zkontrolujte aplikace v obou seznamech a zrušte zaškrtnutí u těch, které nechcete použít. Oba seznamy obsahují:

  - **NÁZEV**: informace o certifikátu nebo úplná cesta aplikace
  - **TYPY SOUBORŮ:** Typ souboru aplikace. Může to být EXE, skript, MSI nebo jakékoli permutaci z těchto typů.
  - **ZNEUŽITELNÉ**: Ikona upozornění značí, pokud se konkrétní aplikace může ze strany útočníka obejít vytváří řešení pro přidávání na seznam povolených aplikací. Tyto aplikace doporučujeme před schválením zkontrolovat.
  - **UŽIVATELÉ:** Uživatelé, kterým se doporučuje povolit spuštění aplikace.

5. Jakmile budete s výběry hotovi, vyberte **Vytvořit**. <br>
Po výběru vytvořit Azure Security Center automaticky vytvoří příslušná pravidla nad přidávání na seznam povolených řešení integrované aplikace, které jsou k dispozici na Windows serverech (Applockeru).


> [!NOTE]
> - Služba Security Center potřebuje minimálně dva týdny dat, aby mohla vytvořit základní hodnoty a doplnit jedinečná doporučení pro skupinu virtuálních počítačů. Noví zákazníci služby Security Center úrovně Standard by měli počítat s chováním, kdy se skupiny virtuálních počítačů zpočátku zobrazí na kartě *Žádné doporučení*.
> - Adaptivní řízení aplikací služby Security Center nepodporuje virtuální počítače, které nemají zapnuté zásady AppLockeru objektem zásad skupiny nebo místními zásadami zabezpečení.
> -  Hlediska zabezpečení nejlepší postup Security Center vždy pokusí vytvořit pravidlo vydavatele pro aplikace, které jsou vybrané povolit a pouze v případě, že aplikace neobsahuje informace o vydavateli (tj. není podepsaná), se vytvoří pravidlo cesty vytvoří pro úplnou cestu konkrétní aplikaci.
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>Úpravy a monitorování skupiny s nakonfigurovaným řízením aplikací

1. Pokud chcete upravit a monitorovat skupinu s nakonfigurovaným zásadám vytváření seznamu povolených aplikací, vraťte se na **adaptivní řízení aplikací** stránku a vybrat **NAKONFIGUROVÁNO** pod **skupiny virtuálních počítačů**:

  ![Skupiny](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

  Seznam obsahuje:

  - **Název**: Název předplatného a skupiny
  - **VIRTUÁLNÍ POČÍTAČE:** Počet virtuálních počítačů ve skupině.
  - **Režim**: režimu Audit se budou protokolovat pokusy o spuštění není na seznamu povolených aplikací; Vynutit bude neumožní spouštění aplikací seznamu povolených
  - **Výstrahy**: všechna aktuální porušení pravidel

2. Klikněte na skupinu a provést změny **upravit zásadu řízení aplikací** stránky.

  ![Ochrana](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

3. V části **Režim ochrany** máte na výběr mezi následujícími možnostmi:

  - **Audit:** V tomto režimu řešení řízení aplikací nevynucuje pravidla a audituje jenom aktivitu na chráněných virtuálních počítačích. Tato možnost se doporučuje pro scénáře, kdy chcete nejprve sledovat celkové chování před tím, než zablokujete spouštění aplikace na cílovém virtuálním počítači.
  - **Vynucení:** V tomto režimu řešení řízení aplikací vynucuje pravidla a zajišťuje zablokování aplikací, které nemají povolené spouštění.

   > [!NOTE]
   > Jak už jsme zmínili, ve výchozím nastavení se nová zásada řízení aplikací vždy nakonfiguruje v režimu *Audit*. 
   >

4. V části **rozšíření zásady**, můžete přidat libovolnou aplikaci cestu, kterou chcete povolit. Po přidání těchto cest, Security Center aktualizuje zásady whielisting aplikací na virtuálních počítačích v rámci vybrané skupiny virtuálních počítačů a vytvoří příslušná pravidla pro tyto aplikace pravidla, která jsou už zavedené.

5. Zkontrolujte aktuální porušení pravidel uvedených v **nedávné** oddílu. Klikněte na každý řádek přesměrování **výstrahy** stránku v rámci Azure Security Center a zobrazit všechny výstrahy, které byly zjištěny službou Azure Security Center na přidružených virtuálních počítačích.
  - **Výstrahy**: Zaprotokolovaná porušení pravidel.
  - **Ne. virtuální počítače**: počet virtuálních počítačů s tímto typem upozornění.

6. V části **pravidla přidávání vydavatele na seznam povolených**, **pravidla přidávání cest na seznam povolených**, a **pravidla přidávání na seznam povolených algoritmu Hash** uvidíte pravidla jsou v tuto chvíli které seznamu povolených aplikací nakonfigurovaný na virtuálních počítačích v rámci skupiny, podle typů kolekcí pravidel. Pro každé pravidlo můžete zobrazit:

  - **Pravidlo**: konkrétní parametry, podle kterých je aplikace vyšetřených funkcí Applockeru k určení, pokud je povoleno spuštění aplikace.
  - **Typ souboru**: typy souborů, které se vztahuje konkrétní pravidlo. Tou může být kterákoli z následujících akcí: EXE, skript, MSI nebo jakékoli permutaci tyto typy souborů.
  - **Uživatelé**: název nebo počet uživatelů, kteří mají povoleno spouštění aplikace, která se vztahuje pravidlo na seznam povolených aplikací.

   ![Pravidla přidávání na seznam povolených](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

7. Klikněte na tři tečky na konci každého řádku, pokud chcete konkrétní pravidlo odstranit nebo upravit povolené uživatele.

8. Po provedení změn **adaptivní řízení aplikací** zásady, klikněte na tlačítko **Uložit**.

### <a name="not-recommended-list"></a>Seznam nedoporučených

Security Center doporučuje jenom zásady na seznam povolených aplikací pro virtuální počítače provozované se spouští stabilní sada aplikací. Pokud se aplikace na přidružených virtuálních počítačích neustále mění, doporučení se nevytvoří.

![Doporučení](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

Seznam obsahuje:
- **NÁZEV:** Název předplatného a skupiny.
- **VIRTUÁLNÍ POČÍTAČE:** Počet virtuálních počítačů ve skupině.

Azure Security Center vám umožní definovat zásady na seznam povolených aplikací v jiných doporučuje i pro skupinu virtuálních počítačů. Postupujte podle stejné zásady, jak bylo popsáno, nakonfigurovat zásadu na seznam povolených aplikací na těchto skupin také.


## <a name="next-steps"></a>Další postup
V tomto dokumentu jste zjistili, jak použít adaptivní řízení aplikací v Azure Security Center k přidávání aplikací spuštěných na virtuálních počítačích Azure na seznam povolených aplikací. Pokud se o službě Azure Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Správa a zpracování výstrah zabezpečení ve službě Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Zjistěte, jak spravovat výstrahy a reagovat na incidenty zabezpečení ve službě Security Center.
* [Monitorování stavu zabezpečení ve službě Azure Security Center](security-center-monitoring.md). Zjistěte, jak monitorovat stav svých prostředků Azure.
* [Principy výstrah zabezpečení ve službě Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Seznamte se s dalšími typy výstrah zabezpečení.
* [Průvodce odstraňováním potíží pro službu Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Zjistěte, jak řešit běžné problémy ve službě Security Center.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md). Přečtěte si nejčastější dotazy o použití této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/). Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.

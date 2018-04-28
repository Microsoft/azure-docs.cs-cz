---
title: Adaptivní řízení aplikací v Azure Security Center | Microsoft Docs
description: Tento dokument vám pomůže použít adaptivní řízení aplikací v Azure Security Center k přidávání aplikací spuštěných na virtuálních počítačích Azure na seznam povolených.
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: ''
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/15/2018
ms.author: yurid
ms.openlocfilehash: 04f557d30f9b7f76bdb2a596bc3e96873876061f
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="adaptive-application-controls-in-azure-security-center-preview"></a>Adaptivní řízení aplikací v Azure Security Center (Preview)
Pomocí tohoto názorného postupu zjistíte, jak nakonfigurovat řízení aplikací v Azure Security Center.

## <a name="what-are-adaptive-application-controls-in-security-center"></a>Co je adaptivní řízení aplikací ve službě Security Center?
Adaptivní řízení aplikací pomáhá řídit, které aplikace se můžou spouštět na vašich virtuálních počítačích v Azure, což kromě dalších výhod posiluje ochranu virtuálních počítačů před malwarem. Služba Security Center pomocí strojového učení analyzuje aplikace spuštěné na virtuálním počítači a pomáhá s aplikováním pravidel přidávání na seznam povolených na základě těchto informací. Tato funkce výrazně zjednodušuje proces konfigurace a správy seznamů povolených aplikací a umožňuje:

- Blokovat nebo upozorňovat na pokusy o spuštění škodlivých aplikací, a to včetně aplikací, které by antimalwarová řešení jinak mohla vynechat.
- Dodržovat zásady zabezpečení vaší organizace, které vyžadují používání pouze licencovaného softwaru.
- Zamezit používání nežádoucího softwaru ve vašem prostředí.
- Zamezit spouštění starých a nepodporovaných aplikací.
- Zabránit používání konkrétních softwarových nástrojů, které nejsou ve vaší organizaci povolené.
- Umožnit IT oddělení řídit přístup k citlivým datům prostřednictvím používání aplikace.

## <a name="how-to-enable-adaptive-application-controls"></a>Jak povolit adaptivní řízení aplikací?
Adaptivní řízení aplikací pomáhá definovat sadu aplikací, které mají povoleno spouštění v nakonfigurovaných skupinách prostředků. Tato funkce je dostupná pouze pro počítače s Windows (všechny verze, Classic nebo Azure Resource Manager). Pokud chcete ve službě Security Center nakonfigurovat přidávání aplikací na seznam povolených, můžete postupovat podle následujících kroků:

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
  - **STAV:** Stav doporučení, který ve většině případů bude otevřený.
  - **ZÁVAŽNOST:** Úroveň závažnosti doporučení.

2. Výběrem skupiny otevřete možnost **Vytvořit pravidla řízení aplikací**.

  ![Pravidla řízení aplikací](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)

3. V části **Vybrat virtuální počítače** zkontrolujte seznam doporučených virtuálních počítačů a zrušte zaškrtnutí těch, na které nechcete použít řízení aplikací. Dále se zobrazí dva seznamy:

  - **Doporučené aplikace:** Seznam aplikací, které se často vyskytují na virtuálních počítačích v této skupině, a proto pro ně Security Center doporučuje pravidla řízení aplikací.
  - **Další aplikace:** Seznam aplikací, které se na virtuálních počítačích v této skupině vyskytují méně často nebo se o nich ví, že jsou zneužitelné (další informace najdete níže), a které se před použitím pravidel doporučuje zkontrolovat.

4. Zkontrolujte aplikace v obou seznamech a zrušte zaškrtnutí u těch, které nechcete použít. Oba seznamy obsahují:

  - **NÁZEV:** Informace o certifikátu aplikace nebo úplná cesta k certifikátu v aplikaci.
  - **TYPY SOUBORŮ:** Typ souboru aplikace. Může to být EXE, Skript nebo MSI.
  - **ZNEUŽITELNÉ:** Ikona upozornění značí, jestli by útočník mohl aplikace využít k obejití seznamu povolených aplikací. Tyto aplikace doporučujeme před schválením zkontrolovat.
  - **UŽIVATELÉ:** Uživatelé, kterým se doporučuje povolit spuštění aplikace.

5. Jakmile budete s výběry hotovi, vyberte **Vytvořit**.

Služba Security Center ve výchozím nastavení vždy povoluje řízení aplikací v režimu *Audit*. Až zkontrolujete, že seznam povolených aplikací nemá žádný nepříznivý vliv na vaši sadu funkcí, můžete režim změnit na *Vynucení*.

Služba Security Center potřebuje minimálně dva týdny dat, aby mohla vytvořit základní hodnoty a doplnit jedinečná doporučení pro skupinu virtuálních počítačů. Noví zákazníci služby Security Center úrovně Standard by měli počítat s chováním, kdy se skupiny virtuálních počítačů zpočátku zobrazí na kartě *Žádné doporučení*.

> [!NOTE]
> Jako osvědčený postup zabezpečení se služba Security Center vždy pokusí pro aplikace, které by se měly přidat na seznam povolených, vytvořit pravidlo vydavatele, a pouze v případě, že aplikace neobsahuje informace o vydavateli (tj. není podepsaná), se vytvoří pravidlo cesty pro úplnou cestu konkrétního souboru EXE.
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>Úpravy a monitorování skupiny s nakonfigurovaným řízením aplikací

1. Pokud chcete upravit a monitorovat skupinu s nakonfigurovaným řízením aplikací, vraťte se na stránku **Adaptivní řízení aplikací** a vyberte **NAKONFIGUROVÁNO** v části **Skupiny virtuálních počítačů**:

  ![Skupiny](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

  Seznam obsahuje:

  - **NÁZEV:** Název předplatného a skupiny.
  - **VIRTUÁLNÍ POČÍTAČE:** Počet virtuálních počítačů ve skupině.
  - **REŽIM:** V režimu Audit se budou protokolovat pokusy o spuštění aplikací, které nejsou na seznamu povolených. Režim Vynucování neumožní spouštění aplikací, které nejsou na seznamu povolených.
  - **PROBLÉMY:** Libovolná aktuální porušení.

2. Vyberte skupinu, abyste mohli provést změny na stránce **Upravit zásadu řízení aplikací**.

  ![Ochrana](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

3. V části **Režim ochrany** máte na výběr mezi následujícími možnostmi:

  - **Audit:** V tomto režimu řešení řízení aplikací nevynucuje pravidla a audituje jenom aktivitu na chráněných virtuálních počítačích. Tato možnost se doporučuje pro scénáře, kdy chcete nejprve sledovat celkové chování před tím, než zablokujete spouštění aplikace na cílovém virtuálním počítači.
  - **Vynucení:** V tomto režimu řešení řízení aplikací vynucuje pravidla a zajišťuje zablokování aplikací, které nemají povolené spouštění.

  Jak už jsme zmínili, ve výchozím nastavení se nová zásada řízení aplikací vždy nakonfiguruje v režimu *Audit*. V části **Rozšíření zásady** můžete přidat vlastní cesty aplikací, které chcete přidat na seznam povolených. Po přidání těchto cest služba Security Center pro tyto aplikace vytvoří patřičná pravidla a přidá je k již existujícím pravidlům.

  V části **Poslední problémy** jsou uvedena všechna aktuální porušení pravidel.

  ![Problémy](./media/security-center-adaptive-application/security-center-adaptive-application-fig7.png)

  Tento seznam obsahuje:
  - **PROBLÉMY:** Všechna zaprotokolovaná porušení pravidel, která můžou zahrnovat následující:

      - **Zablokovaná porušení:** Když je řešení spuštěné v režimu Vynucení a o spuštění se pokusí aplikace, která není na seznamu povolených.
      - **Auditovaná porušení:** Když je řešení spuštěné v režimu Audit a spustí se aplikace, která není na seznamu povolených.

 - **POČET VIRTUÁLNÍCH POČÍTAČŮ:** Počet virtuálních počítačů s tímto typem problému.

  Pokud kliknete na některý z těchto řádků, budete přesměrováni na stránku [Protokol aktivit Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), kde se zobrazí informace o všech virtuálních počítačích s daným typem porušení pravidel. Pokud kliknete na tři tečky na konci každého řádku, můžete příslušnou položku odstranit. V části **Nakonfigurované virtuální počítače** jsou uvedené virtuální počítače, na které se tato pravidla vztahují.

  ![Nakonfigurované virtuální počítače](./media/security-center-adaptive-application/security-center-adaptive-application-fig8.png)

  V části **Pravidla přidávání vydavatele na seznam povolených** tento seznam obsahuje:

  - **PRAVIDLO:** Aplikace, pro které se vytvořilo pravidlo vydavatele na základě informací o certifikátu nalezeného pro příslušnou aplikaci.
  - **TYP SOUBORU:** Typy souborů, na které se vztahuje konkrétní pravidlo vydavatele. Může to být EXE, Skript nebo MSI.
  - **UŽIVATELÉ:** Počet uživatelů, kteří mají povolené spuštění jednotlivých aplikací.

  Další informace najdete v tématu [Principy pravidel vydavatele v AppLockeru](https://docs.microsoft.com/windows/device-security/applocker/understanding-the-publisher-rule-condition-in-applocker).

  ![Pravidla přidávání na seznam povolených](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

  Pokud kliknete na tři tečky na konci každého řádku, můžete příslušné pravidlo odstranit nebo upravit povolené uživatele.

  V části **Pravidla přidávání cest na seznam povolených** je uvedená úplná cesta k aplikaci (včetně konkrétního typu souboru) pro aplikace, které nejsou podepsané digitálním certifikátem, ale stále jsou aktuální v pravidlech přidávání na seznam povolených.

  > [!NOTE]
  > Jako osvědčený postup zabezpečení se služba Security Center ve výchozím nastavení vždy pokusí pro soubory EXE, které by se měly přidat na seznam povolených, vytvořit pravidlo vydavatele, a pouze v případě, že soubor EXE neobsahuje informace o vydavateli (tj. není podepsaný), se vytvoří pravidlo cesty pro úplnou cestu konkrétního souboru EXE.

  ![Pravidla přidávání cest na seznam povolených](./media/security-center-adaptive-application/security-center-adaptive-application-fig10.png)

  Seznam obsahuje:
  - **NÁZEV:** Úplná cesta spustitelného souboru.
  - **TYP SOUBORU:** Typy souborů, na které se vztahuje konkrétní pravidlo cesty. Může to být EXE, Skript nebo MSI.
  - **UŽIVATELÉ:** Počet uživatelů, kteří mají povolené spuštění jednotlivých aplikací.

  Pokud kliknete na tři tečky na konci každého řádku, můžete příslušné pravidlo odstranit nebo upravit povolené uživatele.

4. Po provedení změn na stránce **Adaptivní řízení aplikací** klikněte na tlačítko **Uložit**. Pokud se rozhodnete změny neuplatňovat, klikněte na **Zahodit**.

### <a name="not-recommended-list"></a>Seznam nedoporučených

Služba Security Center doporučuje přidávání aplikací na seznam povolených jenom pro virtuální počítače, na kterých se spouští stabilní sada aplikací. Pokud se aplikace na přidružených virtuálních počítačích neustále mění, doporučení se nevytvoří.

![Doporučení](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

Seznam obsahuje:
- **NÁZEV:** Název předplatného a skupiny.
- **VIRTUÁLNÍ POČÍTAČE:** Počet virtuálních počítačů ve skupině.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak použít adaptivní řízení aplikací v Azure Security Center k přidávání aplikací spuštěných na virtuálních počítačích Azure na seznam povolených aplikací. Pokud se o službě Azure Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Správa a zpracování výstrah zabezpečení ve službě Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Zjistěte, jak spravovat výstrahy a reagovat na incidenty zabezpečení ve službě Security Center.
* [Monitorování stavu zabezpečení ve službě Azure Security Center](security-center-monitoring.md). Zjistěte, jak monitorovat stav svých prostředků Azure.
* [Principy výstrah zabezpečení ve službě Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Seznamte se s dalšími typy výstrah zabezpečení.
* [Průvodce odstraňováním potíží pro službu Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Zjistěte, jak řešit běžné problémy ve službě Security Center.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md). Přečtěte si nejčastější dotazy o použití této služby.
* [Blog o zabezpečení Azure](http://blogs.msdn.com/b/azuresecurity/). Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.

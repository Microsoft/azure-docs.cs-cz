## <a name="view-device-telemetry-in-the-dashboard"></a>Zobrazení telemetrie zařízení na řídicím panelu
Řídicí panel řešení vzdáleného monitorování umožňuje zobrazit telemetrická data, která vaše zařízení odesílají do IoT Hubu.

1. V prohlížeči se vraťte na řídicí panel řešení vzdáleného monitorování a kliknutím na **Zařízení** na levém panelu přejděte k **seznamu zařízení**.
2. V **seznamu zařízení** by se mělo zobrazit, že zařízení je ve stavu **Spuštěno**. Pokud ne, na panelu **Podrobnosti o zařízení** klikněte na **Povolit zařízení**.
   
    ![Zobrazení stavu zařízení][18]
3. Kliknutím na **Řídicí panel** se vraťte na řídicí panel a v rozevíracím seznamu **Zobrazit zařízení** vyberte požadované zařízení. Zobrazí se jeho telemetrická data. Telemetrická data z ukázkové aplikace odpovídají 50 jednotkám pro vnitřní teplotu, 55 jednotkám pro venkovní teplotu a 50 jednotkám pro vlhkost.
   
    ![Zobrazení telemetrie zařízení][img-telemetry]

## <a name="invoke-a-method-on-your-device"></a>Volání metody na zařízení
Řídicí panel řešení vzdáleného monitorování umožňuje vyvolat na zařízení metody prostřednictvím IoT Hubu. V řešení vzdáleného monitorování můžete třeba vyvolat metodu pro simulaci restartování zařízení.

1. Na řídicím panelu řešení vzdáleného monitorování klikněte na **Zařízení** na levém panelu a přejděte k **seznamu zařízení**.
2. V **seznamu zařízení** klikněte na požadované **ID zařízení**.
3. Na panelu **Podrobnosti o zařízení** klikněte na **Metody**.
   
    ![Metody zařízení][13]
4. V rozevíracím seznamu **Metoda** vyberte **InitiateFirmwareUpdate** a potom do **FWPACKAGEURI** zadejte fiktivní adresu URL. Kliknutím na **Vyvolat metodu** vyvoláte tuto metodu na příslušném zařízení.
   
    ![Vyvolání metody zařízení][14]
   

5. Když zařízení zpracovává tuto metodu, zobrazí se zpráva na konzole, kde je spuštěný kód vašeho zařízení. Výsledky metody se přidají do historie na portálu řešení:

    ![Zobrazení historie metod][img-method-history]

## <a name="next-steps"></a>Další postup
Článek [Přizpůsobení předkonfigurovaných řešení][lnk-customize] popisuje některé způsoby, kterými jde tuto ukázku rozšířit. Mezi možná rozšíření patří skutečné senzory a implementace dalších příkazů.

Další informace o [oprávněních najdete na webu azureiotsuite.com][lnk-permissions].

[13]: ./media/iot-suite-v1-visualize-connecting/suite4.png
[14]: ./media/iot-suite-v1-visualize-connecting/suite7-1.png
[18]: ./media/iot-suite-v1-visualize-connecting/suite10.png
[img-telemetry]: ./media/iot-suite-v1-visualize-connecting/telemetry.png
[img-method-history]: ./media/iot-suite-v1-visualize-connecting/history.png
[lnk-customize]: ../articles/iot-suite/iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md
[lnk-permissions]: ../articles/iot-suite/iot-suite-v1-permissions.md

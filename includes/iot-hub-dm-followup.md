## <a name="customize-and-extend-the-device-management-actions"></a>Přizpůsobení a rozšíření akce správy zařízení

Řešení IoT můžete rozbalit definovanou sadu vzorů správy zařízení nebo povolit vlastní vzory pomocí dvojče zařízení a primitiv metoda typu cloud zařízení. Ostatní zařízení správu příklady akcí obnovení továrního nastavení, aktualizace firmwaru, aktualizace softwaru, řízení spotřeby, sítě a připojení správy a šifrování dat.

## <a name="device-maintenance-windows"></a>Časové intervaly pro zařízení

Obvykle konfigurace zařízení k provádění akcí po jednom, které minimalizuje přerušení a výpadků. Časová období údržby zařízení jsou běžně používané vzor k definování čas, kdy zařízení by měl aktualizovat její konfiguraci. Vaše back-end řešení můžete použít požadované vlastnosti dvojčete zařízení k definování a aktivace zásady na zařízení, která umožňuje časového období údržby. Pokud zařízení obdrží zásady okno údržby, můžete ohlášené vlastnosti dvojčete zařízení nahlásit stav zásad. Back endové aplikace pak můžete pomocí dotazů na dvojčata zařízení ověřit dodržování předpisů u zařízení a každou zásadu.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste použili přímé metody k aktivaci vzdálené restartování zařízení. Používají ohlášené vlastnosti k hlášení poslední čas restartování ze zařízení a dotazování dvojčat zařízení ke zjištění poslední čas restartování zařízení z cloudu.

Chcete-li pokračovat v seznamování se službou IoT Hub a schémata správy zařízení jako vzdálené přes aktualizace firmwaru air, naleznete v tématu:

[Kurz: Jak provést upgrade firmwaru][lnk-fwupdate]

Zjistěte, jak rozšířit vaše IoT řešení a plán metoda volá do různých zařízení, najdete v článku [plánování a vysílání úloh] [ lnk-tutorial-jobs] kurzu.

Chcete-li pokračovat v seznamování se službou IoT Hub, přečtěte si téma [Začínáme s IoT Edge][lnk-iot-edge].

[lnk-fwupdate]: ../articles/iot-hub/tutorial-firmware-update.md
[lnk-tutorial-jobs]: ../articles/iot-hub/iot-hub-node-node-schedule-jobs.md
[lnk-iot-edge]: ../articles/iot-edge/tutorial-simulate-device-linux.md
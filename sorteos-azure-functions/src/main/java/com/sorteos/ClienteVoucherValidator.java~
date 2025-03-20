package com.sorteos.functions;

import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;
import com.fasterxml.jackson.databind.ObjectMapper;
import java.net.HttpURLConnection;
import java.net.URL;
import java.util.Map;

public class ClienteVoucherValidator {

    @FunctionName("ClienteVoucherValidator")
    public void run(
            @EventHubTrigger(
                    name = "clienteEvent",
                    eventHubName = "clientes-registro-eventhub",
                    connection = "EventHubConnectionString") String message,
            final ExecutionContext context) {

        ObjectMapper mapper = new ObjectMapper();
        try {
            Map<String, String> evento = mapper.readValue(message, Map.class);
            String clientId = evento.get("id");
            String voucherUrl = evento.get("voucherUrl");
            String type = evento.get("type");

            boolean isValid = validarVoucherOCR(voucherUrl, context);

            String url = "";
            if ("NEW_CLIENT".equals(type)) {
                url = "https://api.sorteosc.com/api/clients/" + (isValid ? "approve/" : "deny/") + "/" + clientId;
            } else if ("RENEWAL".equals(type) && isValid) {
                url = "https://api.sorteosc.com/api/payments/approve-payment/" + "/" + clientId;
            }

            if (!url.isEmpty()) {
                HttpURLConnection con = (HttpURLConnection) new URL(url).openConnection();
                con.setRequestMethod("GET");
                int responseCode = con.getResponseCode();
                context.getLogger().info("Respuesta del endpoint: " + responseCode);
            }

        } catch (Exception ex) {
            context.getLogger().severe("Error procesando evento: " + ex.getMessage());
        }
    }

    private boolean validarVoucherOCR(String voucherUrl, ExecutionContext context) {
        // Aquí implementa la llamada REAL al Azure Cognitive Services OCR
        // Ejemplo simplificado:

        context.getLogger().info("Validando imagen del voucher con OCR: " + voucherUrl);

        // De momento devuelves true para pruebas
        return true;
    }
}

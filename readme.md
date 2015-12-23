spring websocket in the clint 


    @Test
    public void Main() throws InterruptedException, ExecutionException {
        List<Transport> transports = new ArrayList<>();
        StandardWebSocketClient standardWebSocketClient = new StandardWebSocketClient();
        transports.add(new WebSocketTransport(standardWebSocketClient));
        SockJsClient sockJsClient = new SockJsClient(transports);
        WebSocketStompClient stompClient = new WebSocketStompClient(sockJsClient);
        String stompUrl = "ws://localhost:8080/hello";
        stompClient.setMessageConverter(new MappingJackson2MessageConverter());
        ListenableFuture<StompSession> connect = stompClient.connect(stompUrl, new StompSessionHandlerAdapter() {
        });

        connect.addCallback(new ListenableFutureCallback<StompSession>() {
            @Override
            public void onFailure(Throwable ex) {
            }

            @Override
            public void onSuccess(StompSession result) {
                result.subscribe("/topic/greetings", new StompSessionHandlerAdapter() {
                    @Override
                    public Type getPayloadType(StompHeaders headers) {
                        return ConversationMessage.class;
                    }

                    @Override
                    public void handleFrame(StompHeaders headers, Object payload) {
                        ConversationMessage message = (ConversationMessage) payload;
                        System.out.println(message.getContent());
                    }
                });
            }
        });


        connect.addCallback(new ListenableFutureCallback<StompSession>() {
            @Override
            public void onFailure(Throwable ex) {
            }

            @Override
            public void onSuccess(StompSession result) {
                StompHeaders stompHeaders = new StompHeaders();
                stompHeaders.setDestination("/app/hellox");
                ConversationMessage payload = new ConversationMessage();
                payload.setContent("ddddddd");
                result.send(stompHeaders, payload);
            }
        });
        Thread.sleep(4000);
    }
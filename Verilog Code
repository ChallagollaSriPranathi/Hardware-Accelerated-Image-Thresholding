module threshold_logic_stream_32bit (
    input  wire         aclk,
    input  wire         aresetn,

    // Slave side (input stream from PS via DMA)
    input  wire [31:0]  s_axis_tdata,
    input  wire         s_axis_tvalid,
    output wire         s_axis_tready,
    input  wire         s_axis_tlast,

    // Master side (output stream to PS via DMA S2MM)
    output reg  [31:0]  m_axis_tdata,
    output reg          m_axis_tvalid,
    input  wire         m_axis_tready,
    output reg          m_axis_tlast
);

    parameter THRESHOLD = 32'd128;

    // Always ready to accept data if downstream is ready
    assign s_axis_tready = m_axis_tready;

    always @(posedge aclk or negedge aresetn) begin
        if (!aresetn) begin
            m_axis_tdata  <= 32'b0;
            m_axis_tvalid <= 1'b0;
            m_axis_tlast  <= 1'b0;
        end else begin
            if (s_axis_tvalid && s_axis_tready) begin
                m_axis_tdata  <= (s_axis_tdata > THRESHOLD) ? 32'hFF : 32'h00;
                m_axis_tvalid <= 1'b1;
                m_axis_tlast  <= s_axis_tlast;
            end else if (m_axis_tvalid && m_axis_tready) begin
                m_axis_tvalid <= 1'b0;
                m_axis_tlast  <= 1'b0;
            end
        end
    end
endmodule

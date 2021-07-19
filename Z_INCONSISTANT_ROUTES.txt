REPORT z_inconsistant_routes NO STANDARD PAGE HEADING.

TYPES : BEGIN OF t_flow,
          vbeln      TYPE vbap-vbeln,
          posnr      TYPE vbap-posnr,
          route      TYPE vbap-route,
          likp_vbeln TYPE likp-vbeln,
          likp_route TYPE likp-route,
          docno      TYPE /scdl/db_proch_o-docno,
          route_id   TYPE /scdl/db_proch_o-route_id,
        END OF t_flow.
DATA l_flow TYPE t_flow.
DATA lt_flow TYPE TABLE OF t_flow.

SELECT DISTINCT vbap~vbeln, vbap~posnr, vbap~route,
  vbfa~vbeln AS likp_vbeln, likp~route AS likp_route,
  /scdl/db_proch_o~docno, /scdl/db_proch_o~route_id
  FROM vbap INNER JOIN vbfa
  ON vbap~vbeln = vbfa~vbelv AND
     vbap~posnr = vbfa~posnv
  INNER JOIN likp
  ON vbfa~vbeln = likp~vbeln
  INNER JOIN /scdl/db_refdoc
  ON vbfa~vbeln = /scdl/db_refdoc~refdocno
  INNER JOIN /scdl/db_proch_o
  ON /scdl/db_refdoc~docid = /scdl/db_proch_o~docid
  WHERE "vbap~vbeln = '0000035574' AND
  vbfa~vbtyp_v = 'C' AND
  vbfa~vbtyp_n = 'J' AND (
    vbap~route <> likp~route
    OR vbap~route <> /scdl/db_proch_o~route_id
    OR likp~route <>  /scdl/db_proch_o~route_id )
  INTO TABLE @lt_flow
  UP TO 100 ROWS
.

DATA l_out(1000).
LOOP AT lt_flow INTO l_flow.
  CONCATENATE l_flow-vbeln l_flow-posnr l_flow-route
    l_flow-likp_vbeln l_flow-likp_route l_flow-docno l_flow-route_id
    INTO l_out SEPARATED BY '|'.
  WRITE l_out.
ENDLOOP.

# AutoCAD-Plugin-Collection
中文
整理了网络上的各类 CAD 插件，并借助 AI 编写了部分插件。因本人不熟悉代码，整体框架多由 AI 生成，存在不少重复冗余的代码。现分享出来，希望能对有需要的人有所帮助，也欢迎各位技术大佬帮忙精简优化。
English
I’ve collected various CAD plugins online and generated some plugins with AI. Since I’m not familiar with coding, most of the framework was created by AI, resulting in a lot of duplicate and redundant code.This repository is shared in the hope that it will be helpful to those in need. Experienced developers are welcome to help refine and optimize the code.
(PROMPT "\n☆☆启动命令【YYMOYU】☆☆")
(PRINC)
(vl-ACAD-defun (DEFUN C:YYMOYU (/ *ACAD* *DOC* BUTTONS FLAG_SEARCH MSG
				 NOTEMSG STR_SPLIT TITL WYB-PANEL-COUNT
				 WYB-STR-GETFRONTSTR WYB-STR-REGEXPS
				 WYB-SUBLST
			      )
		 (setq FLAG_SEARCH T)
		 (DEFUN STR_SPLIT (STR DELIM / L1 L2)
		   (setq STR (VL-STRING->LIST STR))
		   (setq DELIM (VL-STRING->LIST DELIM))
		   (while (and
			    STR
			  )
		     (if (NOT (MEMBER (CAR STR) DELIM))
		       (PROGN
			 (setq L1 (CONS (CAR STR) L1))
		       )
		       (PROGN
			 (if L1
			   (PROGN
			     (setq L2 (CONS (VL-LIST->STRING
							     (REVERSE L1)
					    ) L2
				      )
			     )
			     (setq L1 nil)
			   )
			 )
		       )
		     )
		     (setq STR (CDR STR))
		   )
		   (if L1
		     (PROGN
		       (setq L2 (CONS (VL-LIST->STRING (REVERSE L1)) L2))
		     )
		   )
		   (REVERSE L2)
		 )
		 (DEFUN WYB-PANEL-COUNT (@TITL @MSG @NOTE @BUTTONS @INIFILE
					       @BWIDTH @ROWNUM / _MAKEPANEL
					       _TRANSBUTTONS G_CMD_LST
					       G_I_CMD G_I_SEC G_LST G_CTL
					       G_CMD G_CT G_KEY G_FLAG
					       WIDTH_MAX LIST_WIDTH_MAX
					       I_ROW_TMP I_COL_TMP
					)
		   (DEFUN _MAKEPANEL (LST KEY / _STRSPLIST _MAKEDCL TMP
					  LST_TMP COLNUM I_BLANK I_ROW I_COL
					  I_CTL I DCLSTR CMD DCL ACT ACTLST
					  DCLFILE CTL
				     )
		     (DEFUN _STRSPLIST (STR / I)
		       (if (setq I (VL-STRING-SEARCH " " STR))
			 (PROGN
			   (LIST (SUBSTR STR 1 I) (VL-STRING-TRIM " "
								  (SUBSTR STR
									  (+ 2 I)
								  )
						  )
			   )
			 )
		       )
		     )
		     (DEFUN _MAKEDCL (LST / FILEID DCLHANDLE)
		       (setq DCLFILE (VL-FILENAME-MKTEMP nil nil ".dcl"))
		       (setq FILEID (OPEN DCLFILE "w"))
		       (COND
			 ((= (TYPE LST) 'STR)
			   (WRITE-LINE LST FILEID)
			 )
			 ((= (TYPE LST) 'LIST)
			   (FOREACH N LST
			     (WRITE-LINE N FILEID)
			   )
			 )
		       )
		       (CLOSE FILEID)
		       (setq DCLHANDLE (LOAD_DIALOG DCLFILE))
		     )
		     (setq G_CMD_LST '("esc"))
		     (setq DCLSTR (STRCAT (VL-STRING-TRANSLATE "$~" "ab"
							       (VL-FILENAME-BASE
										 (VL-FILENAME-MKTEMP)
							       )
					  ) ":dialog{label=\"" @TITL "\";"
				  )
		     )
		     (setq G_I_SEC 1)
		     (setq DCLSTR (STRCAT DCLSTR "\n:row{label=\"\";"))
		     (FOREACH TMP LST
		       (setq DCLSTR (STRCAT DCLSTR "\n:radio_button{ "
					    "fixed_width=true; width=14;"
					    "key=\"s_" (ITOA G_I_SEC) "\"; "
					    "label=\"" (CAR TMP) "\"; " "}"
				    )
		       )
		       (setq G_I_SEC (1+ G_I_SEC))
		     )
		     (setq G_I_SEC (1- G_I_SEC))
		     (setq DCLSTR (STRCAT DCLSTR "}"))
		     (setq DCLSTR (STRCAT DCLSTR "\n:image{ height=0.1; color=140; fixed_height=true;}"))
		     (setq DCLSTR (STRCAT DCLSTR "\n:row{label=\"\";"))
		     (COND
		       ((= KEY -1)
			 (setq I_BLANK 1)
			 (setq G_I_CMD 1)
			 (setq I_ROW 0)
			 (FOREACH LST_TMP LST
			   (setq DCLSTR (STRCAT DCLSTR
						"\n:column{label=\"\";"
					)
			   )
			   (setq WIDTH_MAX 1)
			   (FOREACH TMP (CADR LST_TMP)
			     (setq CMD (_STRSPLIST TMP))
			     (if (> (STRLEN (LAST CMD)) WIDTH_MAX)
			       (PROGN
				 (setq WIDTH_MAX (STRLEN (LAST CMD)))
			       )
			     )
			   )
			   (COND
			     ((= (ATOI (SUBSTR (VER) 13 4)) 2010)
			       (setq @BWIDTH (STRCAT "fixed_width=true; width="
						     (RTOS (+ 6 WIDTH_MAX))
						     "; "
					     )
			       )
			     )
			     ((= (ATOI (SUBSTR (VER) 13 4)) 2012)
			       (setq @BWIDTH (STRCAT "fixed_width=true; width="
						     (RTOS (+ 9 WIDTH_MAX))
						     "; "
					     )
			       )
			     )
			     ((= (ATOI (SUBSTR (VER) 13 4)) 2018)
			       (setq @BWIDTH (STRCAT "fixed_width=true; width="
						     (RTOS (+ 3 WIDTH_MAX))
						     "; "
					     )
			       )
			     )
			     ((= (ATOI (SUBSTR (VER) 13 4)) 2020)
			       (setq @BWIDTH (STRCAT "fixed_width=true; width="
						     (RTOS (+ 3 WIDTH_MAX))
						     "; "
					     )
			       )
			     )
			   )
			   (FOREACH TMP (CADR LST_TMP)
			     (setq CMD (_STRSPLIST TMP))
			     (setq DCLSTR (STRCAT DCLSTR "\n:button{ "
						  @BWIDTH "key=\"c_"
						  (ITOA G_I_CMD) "\"; "
						  "label=\"" (LAST CMD)
						  "\"; " (if (=
								(SUBSTR
									(CAR CMD)
									1 1
								) "~"
							     )
							   (PROGN
							     "is_enabled=false; "
							   )
							   (PROGN
							     ""
							   )
							 ) "}"
					  )
			     )
			     (setq G_CMD_LST (CONS (CAR CMD) G_CMD_LST))
			     (setq G_I_CMD (1+ G_I_CMD))
			     (setq I_ROW (1+ I_ROW))
			   )
			   (REPEAT (- @ROWNUM I_ROW)
			     (setq DCLSTR (STRCAT DCLSTR "\n:button{ "
						  @BWIDTH "key=\"nb"
						  (ITOA I_BLANK) "\"; "
						  "is_enabled=false; " "}"
					  )
			     )
			     (setq I_BLANK (1+ I_BLANK))
			   )
			   (setq I_ROW 0)
			   (setq DCLSTR (STRCAT DCLSTR "}"))
			 )
		       )
		       (KEY (setq I_BLANK 1)
			    (setq G_I_CMD 1)
			    (setq I_ROW 0)
			    (setq I_COL 0)
			    (setq COLNUM (LENGTH LST))
			    (setq LST_TMP (NTH KEY LST))
			    (setq DCLSTR (STRCAT DCLSTR
						 "\n:column{label=\"\";"
					 )
			    )
			    (setq LIST_WIDTH_MAX nil)
			    (setq WIDTH_MAX 1)
			    (setq I_ROW_TMP 0)
			    (setq I_COL_TMP 0)
			    (FOREACH TMP (CADR LST_TMP)
			      (setq CMD (_STRSPLIST TMP))
			      (if (> (STRLEN (LAST CMD)) WIDTH_MAX)
				(PROGN
				  (setq WIDTH_MAX (STRLEN (LAST CMD)))
				)
			      )
			      (setq I_ROW_TMP (1+ I_ROW_TMP))
			      (if (= 1 I_ROW_TMP)
				(PROGN
				  (setq I_COL_TMP (1+ I_COL_TMP))
				)
			      )
			      (if (= I_ROW_TMP @ROWNUM)
				(PROGN
				  (setq I_ROW_TMP 0)
				  (setq LIST_WIDTH_MAX (APPEND
							 LIST_WIDTH_MAX
							 (LIST WIDTH_MAX)
						       )
				  )
				  (setq WIDTH_MAX 1)
				)
			      )
			    )
			    (if (/= I_ROW_TMP 0)
			      (PROGN
				(setq LIST_WIDTH_MAX (APPEND
						       LIST_WIDTH_MAX
						       (LIST WIDTH_MAX)
						     )
				)
			      )
			    ) (REPEAT (- COLNUM I_COL_TMP)
				(setq LIST_WIDTH_MAX (APPEND
						       LIST_WIDTH_MAX
						       (LIST 9)
						     )
				)
			      ) (FOREACH TMP (CADR LST_TMP)
				  (setq CMD (_STRSPLIST TMP))
				  (if (= I_ROW @ROWNUM)
				    (PROGN
				      (setq DCLSTR (STRCAT DCLSTR "}"))
				      (setq DCLSTR (STRCAT DCLSTR "\n:column{label=\"\";"))
				      (setq I_ROW 0)
				    )
				  )
				  (setq I_ROW (1+ I_ROW))
				  (if (= 1 I_ROW)
				    (PROGN
				      (setq I_COL (1+ I_COL))
				    )
				  )
				  (COND
				    ((= (ATOI (SUBSTR (VER) 13 4)) 2010)
				      (setq @BWIDTH (STRCAT "fixed_width=true; width="
							    (RTOS
								  (+ 6
								     (NTH
									  (1- I_COL) LIST_WIDTH_MAX
								     )
								  )
							    ) "; "
						    )
				      )
				    )
				    ((= (ATOI (SUBSTR (VER) 13 4)) 2012)
				      (setq @BWIDTH (STRCAT "fixed_width=true; width="
							    (RTOS
								  (+ 9
								     (NTH
									  (1- I_COL) LIST_WIDTH_MAX
								     )
								  )
							    ) "; "
						    )
				      )
				    )
				    ((= (ATOI (SUBSTR (VER) 13 4)) 2018)
				      (setq @BWIDTH (STRCAT "fixed_width=true; width="
							    (RTOS
								  (+ 3
								     (NTH
									  (1- I_COL) LIST_WIDTH_MAX
								     )
								  )
							    ) "; "
						    )
				      )
				    )
				    ((= (ATOI (SUBSTR (VER) 13 4)) 2020)
				      (setq @BWIDTH (STRCAT "fixed_width=true; width="
							    (RTOS
								  (+ 3
								     (NTH
									  (1- I_COL) LIST_WIDTH_MAX
								     )
								  )
							    ) "; "
						    )
				      )
				    )
				  )
				  (setq DCLSTR (STRCAT DCLSTR "\n:button{ "
						       @BWIDTH "key=\"c_"
						       (ITOA G_I_CMD) "\"; "
						       "label=\""
						       (LAST CMD) "\"; "
						       (if (=
							      (SUBSTR
								      (CAR CMD)
								      1 1
							      ) "~"
							   )
							 (PROGN
							   "is_enabled=false; "
							 )
							 (PROGN
							   ""
							 )
						       ) "}"
					       )
				  )
				  (setq G_CMD_LST (CONS (CAR CMD) G_CMD_LST))
				  (setq G_I_CMD (1+ G_I_CMD))
				)
			    (REPEAT (- @ROWNUM I_ROW)
			      (setq DCLSTR (STRCAT DCLSTR "\n:button{ "
						   @BWIDTH "key=\"nb"
						   (ITOA I_BLANK) "\"; "
						   "is_enabled=false; " "}"
					   )
			      )
			      (setq I_BLANK (1+ I_BLANK))
			    ) (setq DCLSTR (STRCAT DCLSTR "}"))
			    (REPEAT (- COLNUM I_COL)
			      (setq I_COL (1+ I_COL))
			      (COND
				((= (ATOI (SUBSTR (VER) 13 4)) 2010)
				  (setq @BWIDTH (STRCAT "fixed_width=true; width="
							(RTOS (+ 6
								 (NTH
								      (1- I_COL) LIST_WIDTH_MAX
								 )
							      )
							) "; "
						)
				  )
				)
				((= (ATOI (SUBSTR (VER) 13 4)) 2012)
				  (setq @BWIDTH (STRCAT "fixed_width=true; width="
							(RTOS (+ 9
								 (NTH
								      (1- I_COL) LIST_WIDTH_MAX
								 )
							      )
							) "; "
						)
				  )
				)
				((= (ATOI (SUBSTR (VER) 13 4)) 2018)
				  (setq @BWIDTH (STRCAT "fixed_width=true; width="
							(RTOS (+ 3
								 (NTH
								      (1- I_COL) LIST_WIDTH_MAX
								 )
							      )
							) "; "
						)
				  )
				)
			      )
			      (setq DCLSTR (STRCAT DCLSTR
						   "\n:column{label=\"\";"
					   )
			      )
			      (REPEAT @ROWNUM
				(setq DCLSTR (STRCAT DCLSTR "\n:button{ "
						     @BWIDTH "key=\"nb"
						     (ITOA I_BLANK) "\"; "
						     "is_enabled=false; "
						     "}"
					     )
				)
				(setq I_BLANK (1+ I_BLANK))
			      )
			      (setq DCLSTR (STRCAT DCLSTR "}"))
			    )
		       )
		     )
		     (setq DCLSTR (STRCAT DCLSTR "}"))
		     (setq DCLSTR (STRCAT DCLSTR "\n:image{ height=0.1; color=140; fixed_height=true;}"))
		     (setq DCLSTR (STRCAT DCLSTR "\n:text{ "
					  "fixed_width=true; width=true; "
					  "key=\"str\"; " "label=\"春有百花秋有月，夏有凉风冬有雪。若无闲事挂心头，便是人间好时节。\";is_enabled=false; "
					  "}"
				  )
		     )
		     (if @NOTE
		       (PROGN
			 (setq DCLSTR (STRCAT DCLSTR "\n:text{label=\""
					      @NOTE "\";}"
				      )
			 )
		       )
		     )
		     (setq G_CMD_LST (REVERSE G_CMD_LST))
		     (setq DCLSTR (STRCAT DCLSTR "\n:row{label=\"\";"))
		     (if FLAG_SEARCH
		       (PROGN
			 (setq DCLSTR (STRCAT DCLSTR "\n:edit_box{ " "fixed_width=true; width=30; " "key=\"key_editbox_search\"; "
					      "label=\"模糊搜索:\"; " "}"
				      )
			 )
		       )
		     )
		     (setq DCLSTR (STRCAT DCLSTR "\n:spacer{ " "width=12; "
					  "}"
				  )
		     )
		     (setq DCLSTR (STRCAT DCLSTR "\n:button{ "
					  "fixed_width=true; width=10; "
					  "key=\"main\"; "
					  "label=\"主页(&Z)\"; " "}"
				  )
		     )
		     (setq DCLSTR (STRCAT DCLSTR "\n:button{ "
					  "fixed_width=true; width=10; "
					  "key=\"YYDIYDIG\"; "
					  "label=\"命令(&D)\"; " "}"
				  )
		     )
		     (setq DCLSTR (STRCAT DCLSTR "\n:button{ "
					  "fixed_width=true; width=10; "
					  "key=\"HLPE\"; "
					  "label=\"帮助(&Q)\"; " "}"
				  )
		     )
		     (setq DCLSTR (STRCAT DCLSTR "\n:button{ "
					  "fixed_width=true; width=10; "
					  "key=\"Close\"; "
					  "label=\"关闭\"; "
					  "is_default=true; "
					  "is_cancel=true; " "}"
				  )
		     )
		     (setq DCLSTR (STRCAT DCLSTR "}"))
		     (setq DCLSTR (STRCAT DCLSTR "}"))
		     (setq DCL (_MAKEDCL DCLSTR))
		     (setq ACTLST nil)
		     (setq I_CTL 1)
		     (setq I 1)
		     (REPEAT G_I_CMD
		       (setq ACT (STRCAT "(action_tile \"c_" (ITOA I) "\" "
					 "\"(done_dialog " (ITOA I_CTL)
					 ")\")"
				 )
		       )
		       (setq ACTLST (CONS ACT ACTLST))
		       (setq I (1+ I))
		       (setq I_CTL (1+ I_CTL))
		     )
		     (setq I 1)
		     (REPEAT G_I_SEC
		       (setq ACT (STRCAT "(action_tile \"s_" (ITOA I) "\" "
					 "\"(done_dialog " (ITOA I_CTL)
					 ")\")"
				 )
		       )
		       (setq ACTLST (CONS ACT ACTLST))
		       (setq I (1+ I))
		       (setq I_CTL (1+ I_CTL))
		     )
		     (NEW_DIALOG (SUBSTR DCLSTR 1 8) DCL)
		     (COND
		       ((= KEY -1)
			 (setq I 1)
			 (REPEAT G_I_SEC
			   (SET_TILE (STRCAT "s_" (ITOA I)) "0")
			   (setq I (1+ I))
			 )
		       )
		       (KEY (SET_TILE (STRCAT "s_" (ITOA (1+ KEY))) "1"))
		     )
		     (ACTION_TILE "key_editbox_search" (STRCAT "(setq search_text (get_tile \"key_editbox_search\"))(setq flag_search nil)(done_dialog "
							       (ITOA
								     (1+ I_CTL)
							       ) ")"
						       )
		     )
		     (ACTION_TILE "main" (STRCAT "(done_dialog "
						 (ITOA I_CTL) ")"
					 )
		     )
		     (ACTION_TILE "YYDIYDIG" "(done_dialog 1001)")
		     (ACTION_TILE "HLPE" "
			 (alert\"命令提示：
			 \n\n1、输入【1~255】数字，可以更改对象为对应的颜色(【`】为随层)。
			 \n\n2、输入【REINIT】命令，可以修改PGP文件后无需重新启动CAD。
			 \n\n3、【ICL】命令，可以剪切光栅图像。\n\n4、WORD里方框对勾，按住ALT+9745。
			 \n\n5、【模糊搜索】可以输入中文及大写英文，按回车。
			 \n【墨鱼工具箱】。\")")
		     (ACTION_TILE "Close" "(done_dialog 0)")
		     (EVAL (READ (setq TEMP (STRCAT "(progn" (APPLY
							       'STRCAT
							       ACTLST
							     ) ")"
					    )
				 )
			   )
		     )
		     (setq CTL (START_DIALOG))
		     (COND
		       ((= CTL 1001)
			 (C:MOYU_DIY_YY)
		       )
		     )
		     (UNLOAD_DIALOG DCL)
		     (VL-FILE-DELETE DCLFILE)
		     CTL
		   )
		   (DEFUN _TRANSBUTTONS (@BUTTONS KEY / CT_LST CT CMD
						  LST_TMP TMP LST LST1 LST2
						  I
					)
		     (COND
		       ((= KEY -1)
			 (setq LST nil)
			 (FOREACH LST_TMP @BUTTONS
			   (setq LST1 nil)
			   (FOREACH TMP (CADR LST_TMP)
			     (setq CMD (WYB-STR-GETFRONTSTR TMP " " nil))
			     (OR
			       (setq CT 0)
			     )
			     (setq LST1 (CONS (LIST CT TMP) LST1))
			   )
			   (setq LST1 (REVERSE LST1))
			   (setq LST1 (WYB-SUBLST LST1 0 @ROWNUM))
			   (setq LST2 nil)
			   (FOREACH TMP LST1
			     (setq LST2 (CONS (CADR TMP) LST2))
			   )
			   (setq LST2 (LIST (CAR LST_TMP) (REVERSE LST2)))
			   (setq LST (CONS LST2 LST))
			 )
			 (setq LST (REVERSE LST))
		       )
		       (KEY (setq I 0)
			    (setq LST nil)
			    (FOREACH LST_TMP @BUTTONS
			      (if (= I KEY)
				(PROGN
				  (setq LST (CONS LST_TMP LST))
				)
				(PROGN
				  (setq LST (CONS (LIST (CAR LST_TMP)) LST))
				)
			      )
			      (setq I (1+ I))
			    )
			    (setq LST (REVERSE LST))
		       )
		     )
		   )
		   (if @BWIDTH
		     (PROGN
		       (setq @BWIDTH (STRCAT "fixed_width=true; width="
					     (RTOS @BWIDTH) "; "
				     )
		       )
		     )
		     (PROGN
		       (setq @BWIDTH "")
		     )
		   )
		   (OR
		     G_KEY
		     (setq G_KEY -1)
		   )
		   (setq G_LST (_TRANSBUTTONS @BUTTONS G_KEY))
		   (setq G_CTL (_MAKEPANEL G_LST G_KEY))
		   (setq G_FLAG T)
		   (while (and
			    G_FLAG
			  )
		     (COND
		       ((= G_CTL 0)
			 (setq G_FLAG nil)
		       )
		       ((<= G_CTL G_I_CMD)
			 (setq G_FLAG nil)
			 (setq G_CMD (NTH G_CTL G_CMD_LST))
			 (OR
			   (setq G_CT 0)
			 )
			 (COND
			   ((BOUNDP (READ (STRCAT "c:" G_CMD)))
			     (vlax-invoke-method *DOC* 'SENDCOMMAND
						 (STRCAT G_CMD " ")
			     )
			   )
			   ((BOUNDP (READ G_CMD))
			     (vlax-invoke-method *DOC* 'SENDCOMMAND
						 (STRCAT "(" G_CMD ")" " ")
			     )
			   )
			   (T
			     (vlax-invoke-method *DOC* 'SENDCOMMAND
						 (STRCAT G_CMD " ")
			     )
			   )
			 )
		       )
		       ((<= G_CTL (setq TEMP (+ G_I_CMD G_I_SEC)))
			 (setq G_KEY (- G_CTL G_I_CMD 1))
			 (setq G_LST (_TRANSBUTTONS @BUTTONS G_KEY))
			 (setq G_CTL (_MAKEPANEL G_LST G_KEY))
		       )
		       ((NULL FLAG_SEARCH)
			 (setq STR_SEARCH "*")
			 (FOREACH ITEM (STR_SPLIT SEARCH_TEXT " ")
			   (setq STR_SEARCH (STRCAT STR_SEARCH ITEM "*"))
			 )
			 (setq BUTTON_ALL nil)
			 (FOREACH ITEM BUTTONS
			   (FOREACH ITEM1 (CADR ITEM)
			     (if (WCMATCH ITEM1 STR_SEARCH)
			       (PROGN
				 (setq BUTTON_ALL (APPEND
						    BUTTON_ALL
						    (LIST ITEM1)
						  )
				 )
			       )
			     )
			   )
			 )
			 (setq G_CTL (_MAKEPANEL (LIST (LIST "" BUTTON_ALL))
						 0
				     )
			 )
			 (setq FLAG_SEARCH T)
		       )
		       (G_CTL (setq G_KEY -1)
			      (setq G_LST (_TRANSBUTTONS @BUTTONS G_KEY))
			      (setq G_CTL (_MAKEPANEL G_LST G_KEY))
		       )
		     )
		   )
		 )
		 (DEFUN WYB-STR-GETFRONTSTR (STR ST FLAG)
		   (if FLAG
		     (PROGN
		       (setq FLAG "I")
		     )
		     (PROGN
		       (setq FLAG "")
		     )
		   )
		   (CAR (WYB-STR-REGEXPS (STRCAT "[^" ST "]+") STR FLAG))
		 )
		 (DEFUN WYB-SUBLST (@LST @START @LEN / RTN)
		   (setq @LEN (if @LEN
				(PROGN
				  (MIN
				    @LEN
				    (- (LENGTH @LST) @START)
				  )
				)
				(PROGN
				  (- (LENGTH @LST) @START)
				)
			      )
		   )
		   (setq @START (+ @START @LEN))
		   (REPEAT @LEN
		     (setq RTN (CONS (NTH (setq @START (1- @START))
					  @LST
				     ) RTN
			       )
		     )
		   )
		 )
		 (DEFUN WYB-STR-REGEXPS (REGEXP STR KEY / VBSEXP KEYS
						MATCHES X END
					)
		   (setq VBSEXP (vlax-get-or-create-object "VBScript.RegExp"))
		   (vlax-put VBSEXP 'PATTERN REGEXP)
		   (if (NOT KEY)
		     (PROGN
		       (setq KEY "")
		     )
		   )
		   (setq KEY (STRCASE KEY))
		   (setq KEYS '(("I" "IgnoreCase") ("G" "Global")
			  ("M" "Multiline")
			 )
		   )
		   (MAPCAR
		     '(LAMBDA (X)
			(IF (WCMATCH KEY (STRCAT "*" (CAR X) "*"))
			  (vlax-put VBSEXP (READ (CADR X)) 0)
			  (vlax-put VBSEXP (READ (CADR X)) -1)
			)
		      )
		     KEYS
		   )
		   (setq MATCHES (vlax-invoke VBSEXP 'EXECUTE STR))
		   (VLAX-FOR X MATCHES (setq END (CONS
						       (vla-get-Value X) END
						 )
				       )
		   )
		   (REVERSE END)
		 )
		 (setq *ACAD* (vlax-get-acad-object))
		 (setq *DOC* (vla-get-ActiveDocument *ACAD*))
		 (setq DATESTR (MENUCMD "m=$(edtime,$(getvar,date),YYYY年M月D日 HH:MM:SS  DDDD)"))
		 (setq TITL (STRCAT "【墨鱼工具箱】 —— "
				    DATESTR
			    )
		 )
		 (setq MSG "。")
		 (setq NOTEMSG ".")
		 (setq BUTTONS '
		 (("基础工具" (
		 "MOYU_FF_YY 绘制直线" "MOYU_R_YY 绘制矩形"
		 "MOYU_FTY_YY 绘制椭圆" "MOYU_FDB_YY 绘多边形"
		 "MOYU_FDD_YY 绘多段线" "MOYU_W_YY 移动工具"
		 "MOYU_Q_YY 偏移工具" "MOYU_RR_YY 旋转工具"
		 "MOYU_WR_YY 镜像工具" "MOYU_CF_YY 倒角工具"
		 "MOYU_DV_YY 定数等分"
		 )
		 )
		 ("高级工具" (
		 "MOYU_CCX_YY 复制旋转"
		 "MOYU_CCC_YY 连续复制" "MOYU_TC_YY 文字删重"
		 "MOYU_TH_YY 一键统计"  "MOYU_KH_YY 框选统计"
		 "MOYU_TRR_YY 区域删除" "MOYU_FPL_YY 批量倒角"
		 "MOYU_QXN_YY 区域内偏"
		 )
		 )
		 ("标注工具" (
		 "MOYU_DA_YY 线性标注" "MOYU_DC_YY 连续快注"
		 "MOYU_DD_YY 快速标注" "MOYU_DLX_YY 连续标注"
		 "MOYU_DSB_YY 删除标注" "MOYU_DZ_YY 多重引线"
		 "MOYU_DXB_YY 对齐标注" "MOYU_DJD_YY 角度标注"
		 "MOYU_DBJ_YY 半径标注" "MOYU_DZJ_YY 直径标注"
		 "MOYU_DHC_YY 弧长标注" "MOYU_DJX_YY 基线标注"
		 )
		 )
		 ("辅助工具" (
		 "MOYU_XX_YY 绘构造线" "MOYU_XC_YY 横构造线"
		 "MOYU_XZ_YY 竖构造线" "MOYU_Z0_YY Z轴归零"
		 "MOYU_FFX_YY 线改虚线" "MOYU_YM_YY 批量页码"
		 "MOYU_ZZS_YY 置为最上" "MOYU_ZZX_YY 置为最底"
		 "MOYU_ZZ_YY 格式刷"
		 )
		 )
		 ("图块文字" (
		 "MOYU_BB_YY 快速建块" "MOYU_BX_YY 炸开图块"
		 "MOYU_BTJ_YY 图块统计" "MOYU_BGD_YY 改块基点"
		 "MOYU_BQS_YY 按块全选" "MOYU_BGM_YY 图块改名"
		 "MOYU_TTH_YY 文字替换" "MOYU_TZD_YY 单行转多"
		 "MOYU_TT_YY 超级改字" "MOYU_TZK_YY 批改字宽"
)
)
		       )
		 )
		 (WYB-PANEL-COUNT TITL MSG nil BUTTONS "D:\\count.ini" 14 8)
		 (PRINC)
	       )
)
'C:YYMOYU
;;; CHAMFER - MOYU_CF_YY：倒角
(vl-ACAD-defun 
  (DEFUN C:MOYU_CF_YY ()
    (COMMAND "._CHAMFER")
    (PRINC)
  )
)

;;; DIMLINEAR - MOYU_DA_YY：线性标注
(vl-ACAD-defun 
  (DEFUN C:MOYU_DA_YY ()
    (COMMAND "._DIMLINEAR")
    (PRINC)
  )
)

;;; QDIM - MOYU_DD_YY：快速标注
(vl-ACAD-defun 
  (DEFUN C:MOYU_DD_YY ()
    (COMMAND "._QDIM")
    (PRINC)
  )
)

;;; DIMANGULAR - MOYU_DJD_YY：角度标注
(vl-ACAD-defun 
  (DEFUN C:MOYU_DJD_YY ()
    (COMMAND "._DIMANGULAR")
    (PRINC)
  )
)

;;; DIMALIGNED - MOYU_DXB_YY：对齐标注
(vl-ACAD-defun 
  (DEFUN C:MOYU_DXB_YY ()
    (COMMAND "._DIMALIGNED")
    (PRINC)
  )
)

;;; DIMARC - MOYU_DHC_YY：弧长标注
(vl-ACAD-defun 
  (DEFUN C:MOYU_DHC_YY ()
    (COMMAND "._DIMARC")
    (PRINC)
  )
)

;;; DIMRADIUS - MOYU_DBJ_YY：半径标注
(vl-ACAD-defun 
  (DEFUN C:MOYU_DBJ_YY ()
    (COMMAND "._DIMRADIUS")
    (PRINC)
  )
)

;;; DIMDIAMETER - MOYU_DZJ_YY：直径标注
(vl-ACAD-defun 
  (DEFUN C:MOYU_DZJ_YY ()
    (COMMAND "._DIMDIAMETER")
    (PRINC)
  )
)

;;; DIMBASELINE - MOYU_DJX_YY：基线标注
(vl-ACAD-defun 
  (DEFUN C:MOYU_DJX_YY ()
    (COMMAND "._DIMBASELINE")
    (PRINC)
  )
)

;;; DIMCONTINUE - MOYU_DLX_YY：连续标注
(vl-ACAD-defun 
  (DEFUN C:MOYU_DLX_YY ()
    (COMMAND "._DIMCONTINUE")
    (PRINC)
  )
)

;;; MLEADER - MOYU_DZ_YY：多重引线
(vl-ACAD-defun 
  (DEFUN C:MOYU_DZ_YY ()
    (COMMAND "._MLEADER")
    (PRINC)
  )
)

;;; ELLIPSE - MOYU_FTY_YY：椭圆
(vl-ACAD-defun 
  (DEFUN C:MOYU_FTY_YY ()
    (COMMAND "._ELLIPSE")
    (PRINC)
  )
)
;;; POLYGON - MOYU_FDB_YY：多边形
(vl-ACAD-defun 
  (DEFUN C:MOYU_FDB_YY ()
    (COMMAND "._POLYGON")
    (PRINC)
  )
)

;;; LINE - MOYU_FF_YY：直线
(vl-ACAD-defun 
  (DEFUN C:MOYU_FF_YY ()
    (COMMAND "._LINE")
    (PRINC)
  )
)

;;; PLINE - MOYU_FDD_YY：多段线
(vl-ACAD-defun 
  (DEFUN C:MOYU_FDD_YY ()
    (COMMAND "._PLINE")
    (PRINC)
  )
)

;;; OFFSET - MOYU_Q_YY：偏移
(vl-ACAD-defun 
  (DEFUN C:MOYU_Q_YY ()
    (COMMAND "._OFFSET")
    (PRINC)
  )
)

;;; RECTANG - MOYU_R_YY：矩形
(vl-ACAD-defun 
  (DEFUN C:MOYU_R_YY ()
    (COMMAND "._RECTANG")
    (PRINC)
  )
)
;;; ROTATE - MOYU_RR_YY：旋转
(vl-ACAD-defun 
  (DEFUN C:MOYU_RR_YY ()
    (COMMAND "._ROTATE")
    (PRINC)
  )
)

;;; MOVE - MOYU_W_YY：移动
(vl-ACAD-defun 
  (DEFUN C:MOYU_W_YY ()
    (COMMAND "._MOVE")
    (PRINC)
  )
)

;;; MIRROR - MOYU_WR_YY：镜像
(vl-ACAD-defun 
  (DEFUN C:MOYU_WR_YY ()
    (COMMAND "._MIRROR")
    (PRINC)
  )
)

;;; XLINE - MOYU_XX_YY：构造线
(vl-ACAD-defun 
  (DEFUN C:MOYU_XX_YY ()
    (COMMAND "._XLINE")
    (PRINC)
  )
)

;;; MATCHPROP - MOYU_ZZ_YY：属性匹配
(vl-ACAD-defun 
  (DEFUN C:MOYU_ZZ_YY ()
    (COMMAND "._MATCHPROP")
    (PRINC)
  )
)
(vl-ACAD-defun (DEFUN C:MOYU_ZZS_YY ()
		 (PROMPT "【选择对象改为最上层】")
		 (setq SS (SSGET))
		 (command "draworder")
		 (command SS)
		 (command "")
		 (command "f")
		 (PRINC)
	       )
)
'C:MOYU_ZZS_YY
(vl-ACAD-defun (DEFUN C:MOYU_ZZX_YY ()
		 (PROMPT "【选择对象改为最底层】")
		 (setq SS (SSGET))
		 (command "draworder")
		 (command SS)
		 (command "")
		 (command "b")
		 (PRINC)
	       )
)
'C:MOYU_ZZX_YY
(vl-ACAD-defun (DEFUN C:MOYU_XZ_YY ()
		 (PROMPT "【竖向构造线】")
		 (command "XLINE")
		 (command "V")
	       )
)
'C:MOYU_XZ_YY
(vl-ACAD-defun (DEFUN C:MOYU_XC_YY ()
		 (PROMPT "【横向构造线】")
		 (command "XLINE")
		 (command "H")
	       )
)
'C:MOYU_XC_YY
(vl-ACAD-defun (DEFUN C:MOYU_BQS_YY ()
		 (SSSETFIRST nil (SSGET "X" (LIST '(0 . "*") (ASSOC 2
								    (ENTGET
									    (CAR
										 (ENTSEL "【按块全选】")
									    )
								    )
							     )
					    )
				 )
		 )
	       )
)
;;; MATCHPROP - MOYU_DV_YY：定数等分
(vl-ACAD-defun 
  (DEFUN C:MOYU_DV_YY ()
    (COMMAND "._DIVIDE")
    (PRINC)
  )
)
'C:MOYU_BQS_YY

(vl-ACAD-defun (DEFUN C:MOYU_BB_YY (/ BLKNAME END END_DATA I INSPT MAXX0
				     MAXY0 MINX0 MINY0 P1 P2 P3 P4 P5 P6 P7
				     P8 P9 SS
				  )
		 (setq BLKNAME (MENUCMD "M=$(edtime,$(getvar,date),墨鱼工具箱YYYYMODDHHMMSS)"))
		 (if (AND
		       (PRINC "\n选择快速创建块的对象: ")
		       (SETVAR "cmdecho" 0)
		       (setq SS (SSGET))
		     )
		   (PROGN
		     (setq MINX0 1000000.0)
		     (setq MINY0 1000000.0)
		     (setq MAXX0 -1000000.0)
		     (setq MAXY0 -1000000.0)
		     (setq I 0)
		     (REPEAT (SSLENGTH SS)
		       (setq END (SSNAME SS I))
		       (setq END_DATA (ENTGET END))
		       (MIN_MAX)
		       (setq I (1+ I))
		     )
		     (setq P1 (LIST MINX0 MINY0))
		     (command "_.block")
		     (command BLKNAME)
		     (command "non")
		     (command P1)
		     (command SS)
		     (command "")
		     (setq INSPT P1)
		     (command "_.insert")
		     (command BLKNAME)
		     (command "x")
		     (command 1)
		     (command "y")
		     (command 1)
		     (command "z")
		     (command 1)
		     (command "r")
		     (command 0)
		     (command "non")
		     (command INSPT)
		   )
		 )
		 (command "_undo")
		 (command "e")
		 (SETVAR "cmdecho" 1)
		 (PRINC)
	       )
)
'C:MOYU_BB_YY
(DEFUN MIN_MAX (/ MINX MAXX MINY MAXY)
  (vla-GetBoundingBox (vlax-ename->vla-object END) 'MINP 'MAXP)
  (setq MINP (vlax-safearray->list MINP))
  (setq MAXP (vlax-safearray->list MAXP))
  (setq MINX (CAR MINP))
  (setq MAXX (CAR MAXP))
  (setq MINY (CADR MINP))
  (setq MAXY (CADR MAXP))
  (if (> MINX0 MINX)
    (PROGN
      (setq MINX0 MINX)
    )
  )
  (if (> MINY0 MINY)
    (PROGN
      (setq MINY0 MINY)
    )
  )
  (if (< MAXX0 MAXX)
    (PROGN
      (setq MAXX0 MAXX)
    )
  )
  (if (< MAXY0 MAXY)
    (PROGN
      (setq MAXY0 MAXY)
    )
  )
)
(DEFUN EMKBLK (SS PT NAME / I)
  (ENTMAKE (LIST '(0 . "block") (CONS 2 NAME) '(70 . 0) (CONS 10 PT)))
  (REPEAT (setq I (SSLENGTH SS))
    (ENTMAKE (CDR (ENTGET (SSNAME SS (setq I (1- I))))))
  )
  (ENTMAKE '((0 . "ENDBLK")))
  (command "_.erase")
  (command SS)
  (command "")
  (ENTMAKE (LIST '(0 . "INSERT") (CONS 2 NAME) (CONS 10 PT)))
)
(DEFUN SS:BOUNDINGBOX (SS / PTB MINX MINY MAXX MAXY)
  (setq PTB (MAPCAR
	      '(LAMBDA (X)
		 (LIST (LAST (LM:BOUNDINGBOX X)) (CADR
						       (LM:BOUNDINGBOX X)
						 )
		 )
	       )
	      (SSTOLISTF SS)
	    )
  )
  (setq MINX (APPLY
	       'MIN
	       (MAPCAR
		 'CAAR
		 PTB
	       )
	     )
  )
  (setq MINY (APPLY
	       'MIN
	       (MAPCAR
		 '(LAMBDA (X)
		    (CADR (CADR X))
		  )
		 PTB
	       )
	     )
  )
  (setq MAXX (APPLY
	       'MAX
	       (MAPCAR
		 '(LAMBDA (X)
		    (CAR (CADR X))
		  )
		 PTB
	       )
	     )
  )
  (setq MAXY (APPLY
	       'MAX
	       (MAPCAR
		 '(LAMBDA (X)
		    (CADR (CAR X))
		  )
		 PTB
	       )
	     )
  )
  (LIST (LIST MINX MAXY 0) (LIST MAXX MINY))
)
(DEFUN LM:BOUNDINGBOX (ENT / OBJ A B LST)
  (setq OBJ (vlax-ename->vla-object ENT))
  (if (AND
	(vlax-method-applicable-p OBJ 'GETBOUNDINGBOX)
	(NOT (VL-CATCH-ALL-ERROR-P (VL-CATCH-ALL-APPLY 'vla-GetBoundingBox
						       (LIST OBJ 'A 'B)
				   )
	     )
	)
	(setq LST (MAPCAR
		    'vlax-safearray->list
		    (LIST A B)
		  )
	)
      )
    (PROGN
      (MAPCAR
	'(LAMBDA (A)
	   (MAPCAR
	     (QUOTE (LAMBDA (B)
		      ((EVAL B) LST)
		    )
	     )
	     A
	   )
	 )
	'((CAAR CADAR) (CAADR CADAR)
	 (CAADR CADADR)
	 (CAAR CADADR)
	)
      )
    )
  )
)
(DEFUN SSTOLISTF (SS)
  (VL-REMOVE-IF 'LISTP (MAPCAR
			 'CADR
			 (SSNAMEX SS)
		       )
  )
)
(vl-ACAD-defun (DEFUN C:MOYU_BX_YY ()
                 (setvar "cmdecho" 0) ; 关闭命令回显
                 (if (setq ss (ssget '((0 . "INSERT")))) ; 框选块
                   (progn
                     (deep-explode ss) ; 调用递归分解函数
                     (princ "\n完成全部分解操作。")
                   )
                   (princ "\n没有选中任何块。")
                 )
                 (princ)
               )
)

;; 递归分解函数
(defun deep-explode (ss / i blk newss)
  (if ss
    (progn
      (repeat (setq i (sslength ss))
        (setq blk (ssname ss (setq i (1- i))))
        (command "_.EXPLODE" blk)
        ;; 检查是否还有块需要分解
        (if (setq newss (ssget "P" '((0 . "INSERT"))))
          (deep-explode newss) ; 递归分解新生成的块
        )
      )
    )
  )
)
'C:MOYU_BX_YY
(vl-ACAD-defun (DEFUN C:MOYU_BTJ_YY ()
		 (setq ST T)
		 (while (and
			  ST
			)
		   (while (and
			    (NOT (setq ST (ENTSEL "\n选择需要统计的图块:")))
			  )
		   )
		   (if (= (CDR (ASSOC 0 (ENTGET (CAR ST)))) "INSERT")
		     (PROGN
		       (setq BLOCKNAME (CDR (ASSOC 2 (ENTGET (CAR ST)))))
		       (setq ST nil)
		     )
		     (PROGN
		       (PRINC "\n未选择到图块!")
		     )
		   )
		 )
		 (PRINC (STRCAT "\n选择块" BLOCKNAME "<全选>:"))
		 (setq SS (SSGET))
		 (if (= SS nil)
		   (PROGN
		     (setq SS (SSGET "x"))
		   )
		 )
		 (setq N 0)
		 (setq M 0)
		 (while (and
			  (AND
			    SS
			    (< N (SSLENGTH SS))
			  )
			)
		   (setq SSN (SSNAME SS N))
		   (if (= (CDR (ASSOC 0 (ENTGET SSN))) "INSERT")
		     (PROGN
		       (setq BLOCKNAME1 (CDR (ASSOC 2 (ENTGET SSN))))
		       (if (= BLOCKNAME BLOCKNAME1)
			 (PROGN
			   (setq M (+ M 1))
			 )
		       )
		     )
		   )
		   (setq N (+ N 1))
		 )
		 (ALERT (STRCAT "块~" BLOCKNAME "：" (RTOS M 2 0) "个"))
	       )
)
'C:MOYU_BTJ_YY
(vl-ACAD-defun (DEFUN C:MOYU_BGD_YY ()
		 (LM:CHANGEBLOCKBASEPOINT T)
	       )
)
'C:MOYU_BGD_YY
(DEFUN LM:CHANGEBLOCKBASEPOINT (FLG / *ERROR* BLN CMD ENT LCK MAT NBP VEC)
  (DEFUN *ERROR* (MSG)
    (FOREACH LAY LCK
      (vla-put-Lock LAY :vlax-true)
    )
    (if (= 'INT (TYPE CMD))
      (PROGN
	(SETVAR 'CMDECHO CMD)
      )
    )
    (LM:ENDUNDO (LM:ACDOC))
    (if (NOT (WCMATCH (STRCASE MSG T) "*break,*cancel*,*exit*"))
      (PROGN
	(PRINC (STRCAT "\nError: " MSG))
      )
    )
    (PRINC)
  )
  (SETVAR 'ERRNO 0)
  (setq ENT (CAR (ENTSEL "\n 选择一个图块：")))
  (while (and
	   (COND
	     ((= 7 (GETVAR 'ERRNO))
	       (PRINC "\nMissed, try again.")
	     )
	     ((= 'ENAME (TYPE ENT))
	       (if (/= "INSERT" (CDR (ASSOC 0 (ENTGET ENT))))
		 (PROGN
		   (PRINC "\nSelected object is not a block.")
		 )
	       )
	     )
	   )
	 )
  )
  (if (AND
	(= 'ENAME (TYPE ENT))
	(setq NBP (GETPOINT "\n 指定图块新的基点："))
      )
    (PROGN
      (setq MAT (CAR (REVREFGEOM ENT)))
      (setq VEC (MXV MAT (MAPCAR
			   '-
			   (TRANS NBP 1 0)
			   (TRANS (CDR (ASSOC 10 (ENTGET ENT))) ENT 0)
			 )
		)
      )
      (setq BLN (LM:BLOCKNAME (vlax-ename->vla-object ENT)))
      (LM:STARTUNDO (LM:ACDOC))
      (VLAX-FOR LAY (vla-get-Layers (LM:ACDOC)) (if (= :vlax-true
						       (vla-get-Lock LAY)
						    )
						  (PROGN
						    (vla-put-Lock LAY :vlax-false)
						    (setq LCK (CONS LAY LCK))
						  )
						)
      )
      (VLAX-FOR OBJ (vla-Item (vla-get-Blocks (LM:ACDOC)) BLN)
		(vlax-invoke OBJ 'MOVE VEC '(0.0 0.0 0.0))
      )
      (if FLG
	(PROGN
	  (VLAX-FOR BLK (vla-get-Blocks (LM:ACDOC)) (if (= :vlax-false
							   (vla-get-IsXRef BLK)
							)
						      (PROGN
							(VLAX-FOR OBJ BLK
								  (if
								    (AND
								      (= "AcDbBlockReference"
									 (vla-get-ObjectName OBJ)
								      )
								      (= BLN
									 (LM:BLOCKNAME OBJ)
								      )
								      (vlax-write-enabled-p OBJ)
								    )
								    (PROGN
								      (vlax-invoke OBJ 'MOVE '
										   (0.0 0.0 0.0)
										   (MXV
											(CAR
											     (REFGEOM
												      (vlax-vla-object->ename OBJ)
											     )
											) VEC
										   )
								      )
								    )
								  )
							)
						      )
						    )
	  )
	)
      )
      (if (= 1 (CDR (ASSOC 66 (ENTGET ENT))))
	(PROGN
	  (setq CMD (GETVAR 'CMDECHO))
	  (SETVAR 'CMDECHO 0)
	  (VL-CMDF "_.attsync" "_N" BLN)
	  (SETVAR 'CMDECHO CMD)
	)
      )
      (FOREACH LAY LCK
	(vla-put-Lock LAY :vlax-true)
      )
      (vla-Regen (LM:ACDOC) acAllViewports)
      (LM:ENDUNDO (LM:ACDOC))
    )
  )
  (PRINC)
)
(DEFUN REFGEOM (ENT / ANG ENX MAT OCS)
  (setq ENX (ENTGET ENT))
  (setq ANG (CDR (ASSOC 50 ENX)))
  (setq OCS (CDR (ASSOC 210 ENX)))
  (LIST (setq MAT (MXM (MAPCAR
			 '(LAMBDA (V)
			    (TRANS V 0 OCS T)
			  )
			 '((1.0 0.0 0.0) (0.0 1.0 0.0)
			  (0.0 0.0 1.0)
			 )
		       ) (MXM (LIST (LIST (COS ANG) (- (SIN ANG)) 0.0)
				    (LIST (SIN ANG) (COS ANG) 0.0) '
				    (0.0 0.0 1.0)
			      ) (LIST (LIST (CDR (ASSOC 41 ENX)) 0.0 0.0)
				      (LIST 0.0 (CDR (ASSOC 42 ENX)) 0.0)
				      (LIST 0.0 0.0 (CDR (ASSOC 43 ENX)))
				)
			 )
		  )
	)
	(MAPCAR
	  '-
	  (TRANS (CDR (ASSOC 10 ENX)) OCS 0)
	  (MXV MAT (CDR (ASSOC 10 (TBLSEARCH "block" (CDR (ASSOC 2 ENX))))))
	)
  )
)
(DEFUN REVREFGEOM (ENT / ANG ENX MAT OCS)
  (setq ENX (ENTGET ENT))
  (setq ANG (CDR (ASSOC 50 ENX)))
  (setq OCS (CDR (ASSOC 210 ENX)))
  (LIST (setq MAT (MXM (LIST (LIST (/ 1.0 (CDR (ASSOC 41 ENX))) 0.0 0.0)
			     (LIST 0.0 (/ 1.0 (CDR (ASSOC 42 ENX))) 0.0)
			     (LIST 0.0 0.0 (/ 1.0 (CDR (ASSOC 43 ENX))))
		       ) (MXM (LIST (LIST (COS ANG) (SIN ANG) 0.0)
				    (LIST (- (SIN ANG)) (COS ANG) 0.0) '
				    (0.0 0.0 1.0)
			      ) (MAPCAR
				  '(LAMBDA (V)
				     (TRANS V OCS 0 T)
				   )
				  '((1.0 0.0 0.0) (0.0 1.0 0.0)
				   (0.0 0.0 1.0)
				  )
				)
			 )
		  )
	)
	(MAPCAR
	  '-
	  (CDR (ASSOC 10 (TBLSEARCH "block" (CDR (ASSOC 2 ENX)))))
	  (MXV MAT (TRANS (CDR (ASSOC 10 ENX)) OCS 0))
	)
  )
)
(DEFUN MXV (M V)
  (MAPCAR
    '(LAMBDA (R)
       (APPLY
	 (QUOTE +)
	 (MAPCAR
	   (QUOTE *)
	   R
	   V
	 )
       )
     )
    M
  )
)
(DEFUN MXM (M N)
  ((lambda (A)
     (MAPCAR
       '(LAMBDA (R)
	  (MXV A R)
	)
       M
     )
   )
   (TRP N)
  )
)
(DEFUN TRP (M)
  (APPLY
    'MAPCAR
    (CONS 'LIST M)
  )
)
(DEFUN LM:BLOCKNAME (OBJ)
  (if (vlax-property-available-p OBJ 'EFFECTIVENAME)
    (PROGN
      (DEFUN LM:BLOCKNAME (OBJ)
	(vla-get-EffectiveName OBJ)
      )
    )
    (PROGN
      (DEFUN LM:BLOCKNAME (OBJ)
	(vla-get-Name OBJ)
      )
    )
  )
  (LM:BLOCKNAME OBJ)
)
(DEFUN LM:STARTUNDO (DOC)
  (LM:ENDUNDO DOC)
  (vla-StartUndoMark DOC)
)
(DEFUN LM:ENDUNDO (DOC)
  (while (and
	   (= 8 (LOGAND 8 (GETVAR 'UNDOCTL)))
	 )
    (vla-EndUndoMark DOC)
  )
)
(DEFUN LM:ACDOC ()
  (EVAL (LIST 'DEFUN 'LM:ACDOC nil (vla-get-ActiveDocument
							   (vlax-get-acad-object)
				   )
	)
  )
  (LM:ACDOC)
)
(VL-LOAD-COM)
(vl-ACAD-defun (DEFUN C:MOYU_BGM_YY (/ ENT NAME NAME1 DCLNAME TEMPNAME FILEN
				     STREAM DCL_RE DLG NEW OBJ BLOCKS BLKNAM
				     BLOCK DOC
				  )
		 (if (AND
		       (setq ENT (CAR (ENTSEL "\n选择需要改名的块: ")))
		       (OR
			 (EQ "INSERT" (CDR (ASSOC 0 (ENTGET ENT))))
			 (ALERT "没有选择块!")
		       )
		     )
		   (PROGN
		     (setq NAME (CDR (ASSOC 2 (ENTGET ENT))))
		     (setq TEMPNAME (VL-FILENAME-MKTEMP "re-dcl-tmp.dcl"))
		     (setq DCLNAME (COND
				     ((setq FILEN (OPEN TEMPNAME "w"))
				       (FOREACH STREAM '("\n"
					  "RENAME:dialog {\n"
					  "    label = \"修改块名\" ;\n"
					  "    :row {\n"
					  "        :edit_box {\n" "            key = \"dcl_edit_name\" ;\n"
					  "            width = 30 ;\n"
					  "        }\n"
					  "    }\n" "    :row {\n"
					  "        :button {\n" "            key = \"btn_ok\" ;\n"
					  "            label = \"确认\" ;\n"
					  "        }\n"
					  "        :button {\n" "            is_cancel = true ;\n"
					  "            key = \"btn_cancle\" ;\n" "            label = \"取消\" ;\n"
					  "        }\n" "    }\n"
					  "}\n"
					 )
					 (PRINC STREAM FILEN)
				       )
				       (CLOSE FILEN)
				       TEMPNAME
				     )
				   )
		     )
		     (setq DOC (vla-get-ActiveDocument
						       (vlax-get-acad-object)
			       )
		     )
		     (setq OBJ (vlax-ename->vla-object ENT))
		     (setq BLOCKS (vla-get-Blocks DOC))
		     (setq DCL_RE (LOAD_DIALOG DCLNAME))
		     (if (NOT (NEW_DIALOG "RENAME" DCL_RE))
		       (PROGN
			 (EXIT)
		       )
		     )
		     (SET_TILE "dcl_edit_name" NAME)
		     (MODE_TILE "dcl_edit_name" 2)
		     (ACTION_TILE "dcl_edit_name" "(setq name1 $value)")
		     (ACTION_TILE "btn_ok" "(if (=(vla-get-objectname obj) \"AcDbBlockReference\")\n                               (if (tblsearch \"block\" name1)\n                                 (alert (strcat \"块名: \" name1 \" 已经存在.\"))\n                                 (if (not (snvalid name1))\n                                   (alert (strcat \"错误的块名: \" name1))\n                                   (done_dialog 1)\n                                 )\n                               )\n                             )")
		     (setq DLG (START_DIALOG))
		     (if (= DLG 1)
		       (PROGN
			 (setq BLKNAM (vla-get-Name OBJ))
			 (setq BLOCK (vla-Item BLOCKS BLKNAM))
			 (vla-put-Name BLOCK NAME1)
			 (if (= (SUBSTR BLKNAM 1 2) "*U")
			   (PROGN
			     (PRINC "\n这是一个匿名块.")
			     (vla-AuditInfo DOC :vlax-true)
			     (vla-put-Name BLOCK NAME1)
			   )
			 )
			 (PRINC (STRCAT "\n图块 \"" NAME "\" 重命名为 \""
					NAME1 "\""
				)
			 )
		       )
		     )
		     (UNLOAD_DIALOG DCL_RE)
		     (VL-FILE-DELETE DCLNAME)
		   )
		 )
		 (PRINC)
	       )
)
'C:MOYU_BGM_YY
(DEFUN XYP-GET-DXF (CODE ENAME / ENT LST A)
  (if (= (TYPE CODE) 'LIST)
    (PROGN
      (setq ENT (ENTGET ENAME))
      (setq LST nil)
      (FOREACH A CODE
	(setq LST (CONS (LIST A (CDR (ASSOC A ENT))) LST))
      )
      (REVERSE LST)
    )
    (PROGN
      (if (= CODE -3)
	(PROGN
	  (CDR (ASSOC CODE (ENTGET ENAME '("*"))))
	)
	(PROGN
	  (CDR (ASSOC CODE (ENTGET ENAME)))
	)
      )
    )
  )
)
(DEFUN C:MOYU_DC_YY ()
  (command "dimlinear")
  (command PAUSE)
  (command PAUSE)
  (command PAUSE)
  (command "dimcontinue")
  (PRINC)
)
'C:MOYU_DC_YY
(vl-ACAD-defun (DEFUN C:MOYU_Z0_YY ()
		 (SETVAR "cmdecho" 0)
		 (SETVAR "blipmode" 0)
		 (GRAPHSCR)
		 (PRINC "请选择要归零的实体")
		 (setq S (SSGET))
		 (setq LEN (SSLENGTH S))
		 (setq INDEX 0)
		 (REPEAT LEN
		   (setq A (ENTGET (SSNAME S INDEX)))
		   (setq B10 (ASSOC 10 A))
		   (setq B11 (ASSOC 11 A))
		   (setq X10 (CADR B10))
		   (setq Y10 (CADDR B10))
		   (setq X11 (CADR B11))
		   (setq Y11 (CADDR B11))
		   (setq B101 (CONS 10 (LIST X10 Y10 0)))
		   (setq B111 (CONS 11 (LIST X11 Y11 0)))
		   (setq A (SUBST
			     B101
			     B10
			     A
			   )
		   )
		   (ENTMOD A)
		   (setq A (SUBST
			     B111
			     B11
			     A
			   )
		   )
		   (ENTMOD A)
		   (setq INDEX (+ INDEX 1))
		 )
		 (PRINC "选择对象已经成功Z轴归零。")
		 (PRINC)
	       )
)
'C:MOYU_Z0_YY
(vl-ACAD-defun (DEFUN C:MOYU_CCX_YY (/ SS SS1 P1 P2 E I ANG)
		 (setq SS (SSGET))
		 (if (NOT SS)
		   (PROGN
		     (VL-EXIT-WITH-VALUE 0)
		   )
		 )
		 (setq P1 (GETPOINT "\n选择复制基点"))
		 (if (NOT P1)
		   (PROGN
		     (VL-EXIT-WITH-VALUE 0)
		   )
		 )
		 (setq P2 (acet-ss-drag-move SS P1 "\n选择插入点" 1))
		 (setq I 0)
		 (setq SS1 (SSADD))
		 (REPEAT (SSLENGTH SS)
		   (setq E (SSNAME SS I))
		   (setq I (1+ I))
		   (vla-Copy (vlax-ename->vla-object E))
		   (setq E (ENTLAST))
		   (setq SS1 (SSADD E SS1))
		   (vla-Move (vlax-ename->vla-object E)
			     (vlax-3d-point P1) (vlax-3d-point P2)
		   )
		 )
		 (setq ANG (acet-ss-drag-rotate SS1 P2 "\n选输入旋转角度" 1))
		 (setq I 0)
		 (REPEAT (SSLENGTH SS1)
		   (setq E (SSNAME SS1 I))
		   (setq I (1+ I))
		   (vla-Rotate (vlax-ename->vla-object E)
			       (vlax-3d-point P2) ANG
		   )
		 )
	       )
)
'MOYU_CCX_YY
(defun C:MOYU_CCC_YY (/ D EN P0 P1 R SS)
  (YY_KAISHI_YY)
  (SETVAR "CMDECHO" 0)

  ;; 实体选择集函数
  (DEFUN SSNEXT (EN / SS)
    (setq SS (SSADD))
    (while (and
             (setq EN (ENTNEXT EN))
           )
      (if (NOT (MEMBER (CDR (ASSOC 0 (ENTGET EN)))
                   (LIST "ATTRIB" "VERTEX" "SEQEND")
               )
          )
        (PROGN
          (setq SS (SSADD EN SS))
        )
      )
    )
    SS
  )

  ;; 错误处理函数
  (defun *error* (msg)
    (if (/= msg "Function cancelled")
        (princ (strcat "\nError: " msg)))
    (YY_END_YY)
    (setvar "CMDECHO" 1)
    (princ)
  )

  ;; 主程序流程
  (if (setq SS (SSGET))
    (PROGN
      (if (setq P0 (GETPOINT "\n指定基点:"))
        (PROGN
          (while (and
                   T
                )
            (PRINC "\n指定下一点或距离:")
            (if D
              (PROGN
                (SETVAR "osmode" 0)
                (PRINC (STRCAT "<" (RTOS D) ">:"))
              )
            )
            (setq EN (ENTLAST))
            (command ".copy")
            (command SS)
            (command "")
            (command P0)
            (command PAUSE)
            (command ".erase")
            (command (SSNEXT EN))
            (command "")
            (setq P1 (GETVAR "lastpoint"))
            (if (EQUAL P0 P1 1.0e-08)
              (PROGN
                (setq P1 (POLAR P0 R D))
              )
              (PROGN
                (setq D (DISTANCE P0 P1))
                (setq R (ANGLE P0 P1))
              )
            )
            (if (NOT (EQUAL P0 P1 1.0e-08))
              (PROGN
                (setq EN (ENTLAST))
                (command ".copy")
                (command SS)
                (command "")
                (command P0)
                (command P1)
                (setq SS (SSNEXT EN))
                (setq P0 P1)
              )
            )
          )
        )
      )
    )
  )
  (YY_END_YY)
  (PRINC)
)
'C:MOYU_CCC_YY
(defun C:MOYU_TC_YY (/ ent obj txt layer color ss i j e1 e2 o1 o2 c1 c2 d tol del)

  ;; 开始程序
  (YY_KAISHI_YY)
  (SETVAR "CMDECHO" 0)

  ;; 错误处理函数
  (defun *error* (msg)
    (if (/= msg "Function cancelled")
        (princ (strcat "\nError: " msg)))
    (YY_END_YY)
    (setvar "CMDECHO" 1)
    (princ)
  )

  ;; 文字内容清理函数
  (defun tc:clean (s)
    (if s
      (progn
        (setq s (vl-string-subst " " "\\P" s))
        (setq s (vl-string-trim " " s))
        (strcase s)
      )
      ""
    )
  )

  ;; 获取对象中心点
  (defun tc:center (obj)
    (if obj
      (progn
        (vla-GetBoundingBox obj 'p1 'p2)
        (setq p1 (vlax-safearray->list p1))
        (setq p2 (vlax-safearray->list p2))
        (list
          (/ (+ (car p1) (car p2)) 2.0)
          (/ (+ (cadr p1) (cadr p2)) 2.0)
          (if (caddr p1) (/ (+ (caddr p1) (caddr p2)) 2.0) 0.0)
        )
      )
      '(0 0 0) ; 返回默认点
    )
  )

  (setq tol 5.0)
  (setq del 0)

  ;; ① 选择参考文字
  (setq ent (car (nentsel "\n请选择一个参考文字: ")))

  (if (not ent)
    (progn 
      (princ "\n未选择") 
      (YY_END_YY)
      (princ)
    )
    (progn
      ;; 检查是否为文字对象
      (setq obj_data (entget ent))
      (if obj_data
        (progn
          (setq obj_type (cdr (assoc 0 obj_data)))
          
          (if (or (= obj_type "TEXT") (= obj_type "MTEXT"))
            (progn
              (setq obj (vlax-ename->vla-object ent))
              (if obj
                (progn
                  (setq txt (tc:clean (vla-get-TextString obj)))
                  (setq layer (vla-get-Layer obj))
                  (setq color (vla-get-Color obj))

                  ;; ② 获取同图层同颜色所有文字
                  (setq ss (ssget "X"
                            (list
                              (cons 0 "TEXT,MTEXT")
                              (cons 8 layer)
                              (cons 62 color)
                            )
                          ))

                  (if (not ss)
                    (progn 
                      (princ "\n未找到对象") 
                      (YY_END_YY)
                      (princ)
                    )
                    (progn
                      ;; ③ 改进：持续删除直到没有更多重复项
                      (setq continue_delete T)
                      
                      (while continue_delete
                        (setq continue_delete nil)
                        
                        ;; 重新获取符合条件的文字列表
                        (setq txtList '())
                        (setq i 0)
                        (repeat (sslength ss)
                          (setq e1 (ssname ss i))
                          ;; 检查实体是否存在
                          (if (and e1 (entget e1))
                            (progn
                              (setq o1 (vlax-ename->vla-object e1))
                              (if o1
                                (if (= (tc:clean (vla-get-TextString o1)) txt)
                                  (setq txtList (cons e1 txtList))
                                )
                              )
                            )
                          )
                          (setq i (1+ i))
                        )

                        ;; 检查列表是否为空
                        (if (null txtList)
                          (setq continue_delete nil)
                          (progn
                            ;; ④ 改进：双重循环检查重复并删除
                            (setq i 0)
                            (while (and (< i (length txtList)) (not continue_delete))
                              (setq e1 (nth i txtList))

                              ;; 确保实体仍然存在
                              (if (and e1 (entget e1))
                                (progn
                                  (setq o1 (vlax-ename->vla-object e1))
                                  (if o1
                                    (progn
                                      (setq c1 (tc:center o1))

                                      (setq j (+ i 1))

                                      (while (and (< j (length txtList)) (not continue_delete))
                                        (setq e2 (nth j txtList))

                                        ;; 确保实体仍然存在
                                        (if (and e2 (entget e2))
                                          (progn
                                            (setq o2 (vlax-ename->vla-object e2))
                                            (if o2
                                              (progn
                                                (setq c2 (tc:center o2))
                                                (setq d (distance c1 c2))

                                                (if (< d tol)
                                                  (progn
                                                    (entdel e2)
                                                    (setq del (1+ del))
                                                    (setq continue_delete T) ; 标记需要继续删除
                                                  )
                                                )
                                              )
                                            )
                                          )
                                        )

                                        (if (not continue_delete)
                                          (setq j (1+ j))
                                        )
                                      )
                                    )
                                  )
                                )
                              )

                              (if (not continue_delete)
                                (setq i (1+ i))
                              )
                            )
                          )
                        )
                      )

                      (alert (strcat "删除重复数量: " (itoa del)))
                    )
                  )
                )
                (progn
                  (princ "\n无法获取对象信息")
                  (YY_END_YY)
                  (princ)
                )
              )
            )
            (progn
              (princ "\n选择的不是文字对象")
              (YY_END_YY)
              (princ)
            )
          )
        )
        (progn
          (princ "\n无法获取实体数据")
          (YY_END_YY)
          (princ)
        )
      )
    )
  )

  (YY_END_YY)
  (princ)
)
'C:MOYU_TC_YY 文字删重

(defun C:MOYU_TH_YY (/ ent obj txt layer color ss i e o
               objtype pt ptlist count
               linetype total radius)

  ;; 开始程序
  (YY_KAISHI_YY)
  (SETVAR "CMDECHO" 0)

  ;; 错误处理函数
  (defun *error* (msg)
    (if (/= msg "Function cancelled")
        (princ (strcat "\nError: " msg)))
    (YY_END_YY)
    (setvar "CMDECHO" 1)
    (princ)
  )

  ;; MTEXT统一清洗
  (defun th:clean (str)
    (if str
      (progn
        (setq str (vl-string-subst " " "\\P" str))
        (setq str (vl-string-subst " " "\n" str))
        (setq str (vl-string-trim " " str))
        (strcase str)
      )
      ""
    )
  )

  ;; 获取中心点（用于去重）
  (defun th:center (obj)
    (vla-GetBoundingBox obj 'p1 'p2)
    (setq p1 (vlax-safearray->list p1))
    (setq p2 (vlax-safearray->list p2))
    (list
      (/ (+ (car p1) (car p2)) 2.0)
      (/ (+ (cadr p1) (cadr p2)) 2.0)
      0.0
    )
  )

  ;; 判断是否重复点
  (defun th:inlist (pt lst tol)
    (vl-some
      (function
        (lambda (p)
          (< (distance pt p) tol)
        )
      )
      lst
    )
  )

  (setq tol 5.0)
  (setq ptlist '())
  (setq count 0)

  ;; ================= 选择对象 =================
  (setq ent (car (nentsel "\n请选择文字或线条对象: ")))

  (if (not ent)
    (progn 
      (princ "\n未选择") 
      (YY_END_YY)
      (princ)
    )
    (progn
      (setq obj (vlax-ename->vla-object ent))
      (setq layer (vla-get-Layer obj))
      (setq color (vla-get-Color obj))
      (setq objtype (cdr (assoc 0 (entget ent))))

      ;; ================= 文字统计（去重版） =================
      (if (or (= objtype "TEXT") (= objtype "MTEXT"))
        (progn
          (setq txt (th:clean (vla-get-TextString obj)))

          (setq ss (ssget "X"
                    (list
                      (cons 0 "TEXT,MTEXT")
                      (cons 8 layer)
                      (cons 62 color)
                    )
                  ))

          (if ss
            (progn
              (setq i 0)

              (repeat (sslength ss)
                (setq e (ssname ss i))
                (setq o (vlax-ename->vla-object e))

                (if (= (th:clean (vla-get-TextString o)) txt)
                  (progn
                    (setq pt (th:center o))

                    ;; ? 去重核心
                    (if (not (th:inlist pt ptlist tol))
                      (progn
                        (setq ptlist (cons pt ptlist))
                        (setq count (1+ count))
                      )
                    )
                  )
                )

                (setq i (1+ i))
              )

              (alert
                (strcat
                  "文字: " txt "\n"
                  "去重后数量: " (itoa count)
                )
              )
            )
          )
        )

        ;; ================= 线条统计（原逻辑保留） =================
        (if (member objtype '("LINE" "ARC" "CIRCLE" "LWPOLYLINE"))
          (progn
            (setq linetype (vla-get-Linetype obj))
            (setq total 0)
            (setq i 0)

            (setq ss (ssget "X"
                      (list
                        (cons 0 "LINE,ARC,CIRCLE,LWPOLYLINE")
                        (cons 8 layer)
                      )
                    ))

            (if ss
              (progn
                (repeat (sslength ss)
                  (setq e (ssname ss i))
                  (setq o (vlax-ename->vla-object e))

                  (if (and
                        (= (vla-get-Linetype o) linetype)
                        (= (vla-get-Color o) color)
                        (= (vla-get-Layer o) layer)
                      )
                    (cond
                      ((= (cdr (assoc 0 (entget e))) "LINE")
                        (setq total (+ total (vla-get-Length o))))
                      ((= (cdr (assoc 0 (entget e))) "ARC")
                        (setq total (+ total (vla-get-ArcLength o))))
                      ((= (cdr (assoc 0 (entget e))) "CIRCLE")
                        (setq radius (vla-get-Radius o))
                        (setq total (+ total (* 2 pi radius))))
                      ((= (cdr (assoc 0 (entget e))) "LWPOLYLINE")
                        (setq total (+ total (vla-get-Length o))))
                    )
                  )

                  (setq i (1+ i))
                )

                (alert
                  (strcat
                    "总长度: " (rtos total 2 0)
                  )
                )
              )
            )
          )

          (alert "不支持对象")
        )
      )
    )
  )

  (YY_END_YY)
  (princ)
)
'C:MOYU_TH_YY 一键统计

(defun C:MOYU_KH_YY (/ ss ent i ent-item obj-item ent-data obj-type 
                layer-name color text-string count 
                total-length radius linetype
                pt-list pt tol)

  ;; 开始程序
  (YY_KAISHI_YY)
  (SETVAR "CMDECHO" 0)

  ;; 错误处理函数
  (defun *error* (msg)
    (if (/= msg "Function cancelled")
        (princ (strcat "\nError: " msg)))
    (YY_END_YY)
    (setvar "CMDECHO" 1)
    (princ)
  )

  (defun kh:clean (s)
    (if s
      (progn
        (setq s (vl-string-subst " " "\\P" s))
        (setq s (vl-string-trim " " s))
        (strcase s)
      )
      ""
    )
  )

  (defun kh:center (obj)
    (vla-GetBoundingBox obj 'p1 'p2)
    (setq p1 (vlax-safearray->list p1))
    (setq p2 (vlax-safearray->list p2))
    (list
      (/ (+ (car p1) (car p2)) 2.0)
      (/ (+ (cadr p1) (cadr p2)) 2.0)
      0.0
    )
  )

  (defun kh:is-duplicate (pt lst tol)
    (vl-some
      (function
        (lambda (p)
          (< (distance pt p) tol)
        )
      )
      lst
    )
  )

  (defun getTextString (obj)
    (cond
      ((= (vla-get-ObjectName obj) "AcDbText") (vla-get-TextString obj))
      ((= (vla-get-ObjectName obj) "AcDbMText") (vla-get-TextString obj))
      (T "")
    )
  )

  (setq tol 5.0)

  (princ "\n框选对象...")
  (setq ss (ssget))

  (if ss
    (progn
      (setq ent (car (nentsel "\n点选一个样本对象: ")))

      (if ent
        (progn
          (setq ent-data (entget ent))
          (setq obj-type (cdr (assoc 0 ent-data)))
          (setq obj-item (vlax-ename->vla-object ent))
          (setq layer-name (vla-get-Layer obj-item))
          (setq color (vla-get-Color obj-item))

          ;; ================= 文字统计（去重版） =================
          (if (or (= obj-type "TEXT") (= obj-type "MTEXT"))
            (progn
              (setq text-string (kh:clean (getTextString obj-item)))

              (setq count 0)
              (setq pt-list '())
              (setq i 0)

              (repeat (sslength ss)
                (setq ent-item (ssname ss i))
                (setq ent-data (entget ent-item))
                (setq obj-type (cdr (assoc 0 ent-data)))

                (if (or (= obj-type "TEXT") (= obj-type "MTEXT"))
                  (progn
                    (setq obj-item (vlax-ename->vla-object ent-item))

                    (if (and
                          (= (kh:clean (getTextString obj-item)) text-string)
                          (= (vla-get-Layer obj-item) layer-name)
                          (= (vla-get-Color obj-item) color)
                        )
                      (progn
                        (setq pt (kh:center obj-item))

                        ;; ? 核心：去重判断
                        (if (not (kh:is-duplicate pt pt-list tol))
                          (progn
                            (setq pt-list (cons pt pt-list))
                            (setq count (1+ count))
                          )
                        )
                      )
                    )
                  )
                )

                (setq i (1+ i))
              )

              (alert
                (strcat
                  "文字: \"" text-string "\"\n"
                  "去重后数量: " (itoa count)
                )
              )
            )

            ;; ================= 线条统计（不变） =================
            (if (member obj-type '("LINE" "ARC" "CIRCLE" "LWPOLYLINE"))
              (progn
                (setq linetype (vla-get-Linetype obj-item))
                (setq total-length 0)
                (setq i 0)

                (repeat (sslength ss)
                  (setq ent-item (ssname ss i))
                  (setq obj-item (vlax-ename->vla-object ent-item))
                  (setq ent-data (entget ent-item))
                  (setq obj-type (cdr (assoc 0 ent-data)))

                  (if (and
                        (member obj-type '("LINE" "ARC" "CIRCLE" "LWPOLYLINE"))
                        (= (vla-get-Linetype obj-item) linetype)
                        (= (vla-get-Color obj-item) color)
                        (= (vla-get-Layer obj-item) layer-name)
                      )
                    (cond
                      ((= obj-type "LINE")
                        (setq total-length (+ total-length (vla-get-Length obj-item))))
                      ((= obj-type "ARC")
                        (setq total-length (+ total-length (vla-get-ArcLength obj-item))))
                      ((= obj-type "CIRCLE")
                        (setq radius (vla-get-Radius obj-item))
                        (setq total-length (+ total-length (* 2 pi radius))))
                      ((= obj-type "LWPOLYLINE")
                        (setq total-length (+ total-length (vla-get-Length obj-item))))
                    )
                  )

                  (setq i (1+ i))
                )

                (alert (strcat "总长度: " (rtos total-length 2 0)))
              )

              (alert "不支持类型")
            )
          )
        )
      )
    )
  )

  (YY_END_YY)
  (princ)
)
'C:MOYU_KH_YY 框选统计
(DEFUN PLSXPY_YY ()
  (if (SSGET ":s" '((0 . "Arc,Circle,Ellipse,*Line")))
    (PROGN
      (VLAX-FOR OBJ (vla-get-ActiveSelectionSet
						(vla-get-ActiveDocument
									(vlax-get-acad-object)
						)
		    ) (vla-Offset OBJ GETDS) (vla-Offset OBJ (* GETDS -1))
      )
    )
    (PROGN
      (vlax-release-object OBJ)
    )
  )
  (if (NOT (GETPOINT "\n左键保留源对象 <空格删除>"))
    (PROGN
      (command "_.ERASE")
      (command (SSGET "p"))
      (command "")
    )
  )
  (PRINC)
)
(vl-ACAD-defun (DEFUN C:MOYU_QXN_YY ()
		 (PROMPT "【在封闭区域内向内偏移线条】")
		 (YY_KAISHI_YY)
		 (SETVAR "cmdecho" 0)
		 (SETVAR "osmode" 0)
		 (if (NOT SSJ)
		   (PROGN
		     (setq SSJ (GETSTRING "\n请输入偏移量:"))
		   )
		   (PROGN
		     (setq SSJ (if (/= "" (setq SS2K (GETSTRING
								(STRCAT "\n请输入偏移量<"
									SSJ ">:"
								)
						     )
					  )
				   )
				 (PROGN
				   SS2K
				 )
				 (PROGN
				   SSJ
				 )
			       )
		     )
		   )
		 )
		 (while (and
			  (setq PT (GETPOINT "\n闭合范围里取点(退出ESC)："))
			)
		   (command "bpoly")
		   (command PT)
		   (command "")
		   (setq SSAA (ENTLAST))
		   (command "offset")
		   (command SSJ)
		   (command SSAA)
		   (command PT)
		   (command "")
		   (command "_.erase")
		   (command SSAA)
		   (command "")
		 )
		 (YY_END_YY)
		 (PRINC)
	       )
)
'C:MOYU_QXN_YY
(DEFUN MC:SPL (LST / PT)
  (ENTMAKE (APPEND
	     (LIST '(0 . "LWPOLYLINE") '(100 . "AcDbEntity") '(100 . "AcDbPolyline")
		   (CONS 90 (LENGTH LST))
	     )
	     (MAPCAR
	       '(LAMBDA (PT)
		  (CONS 10 PT)
		)
	       LST
	     )
	   )
  )
)
(DEFUN MC:EQ-LINE (PT1 PT2 PT3 PT4 N R / NWW NHH)
  (setq HH (DISTANCE PT1 PT4))
  (setq WW (DISTANCE PT1 PT2))
  (if (= R 0)
    (PROGN
      (setq NWW (/ WW N))
      (REPEAT (1- N)
	(setq PT1 (POLAR PT1 R NWW))
	(setq PT4 (POLAR PT4 R NWW))
	(MC:ZX PT1 PT4)
      )
    )
    (PROGN
      (setq NHH (/ HH N))
      (REPEAT (1- N)
	(setq PT1 (POLAR PT1 R NHH))
	(setq PT2 (POLAR PT2 R NHH))
	(MC:ZX PT1 PT2)
      )
    )
  )
)
(DEFUN MC:RTOSZ (STR)
  (setq STR (RTOS STR 2 0))
)
(DEFUN MC:ZX (PT1 PT2)
  (ENTMAKE (LIST '(0 . "LINE") (CONS 10 PT1) (CONS 11 PT2)))
)
(DEFUN MC:ROZ (R)
  (if (OR
	(< (* 0.25 PI) R (* 0.75 PI))
	(< (* 1.25 PI) R (* 1.75 PI))
      )
    (PROGN
      (setq R (* PI 0.5))
    )
    (PROGN
      (setq R 0)
    )
  )
)
(DEFUN MC:WK (SS / OLDOS OLDLA LST N OBJ MINX MINY MAXX MAXY PT1 PT2 PT3 PT4)
  (setq OLDOS (GETVAR "osmode"))
  (setq OLDLA (GETVAR "clayer"))
  (SETVAR "cmdecho" 0)
  (SETVAR "osmode" 0)
  (REPEAT (setq N (SSLENGTH SS))
    (setq OBJ (vlax-ename->vla-object (SSNAME SS (setq N (1- N)))))
    (vla-GetBoundingBox OBJ 'X 'Y)
    (setq LST (CONS (vlax-safearray->list Y) (CONS
						   (vlax-safearray->list X)
						   LST
					     )
	      )
    )
  )
  (setq MINX (CAR (VL-SORT (MAPCAR
			     'CAR
			     LST
			   ) '<
		  )
	     )
  )
  (setq MINY (CAR (VL-SORT (MAPCAR
			     'CADR
			     LST
			   ) '<
		  )
	     )
  )
  (setq MAXX (CAR (VL-SORT (MAPCAR
			     'CAR
			     LST
			   ) '>
		  )
	     )
  )
  (setq MAXY (CAR (VL-SORT (MAPCAR
			     'CADR
			     LST
			   ) '>
		  )
	     )
  )
  (setq PT1 (LIST MINX MINY))
  (setq PT2 (LIST MAXX MINY))
  (setq PT3 (LIST MAXX MAXY))
  (setq PT4 (LIST MINX MAXY))
  (ENTMAKE (LIST '(0 . "LWPOLYLINE") '(100 . "AcDbEntity") '(100 . "AcDbPolyline")
		 '(90 . 4) '(70 . 1) (CONS 10 PT1) (CONS 10 PT2)
		 (CONS 10 PT3) (CONS 10 PT4)
	   )
  )
  (SETVAR "osmode" OLDOS)
  (SETVAR "clayer" OLDLA)
  (SETVAR "cmdecho" 1)
  (PRINC)
)
(DEFUN LAST_ENT (EN / SS)
  (if EN
    (PROGN
      (setq SS (SSADD))
      (while (and
	       (setq EN (ENTNEXT EN))
	     )
	(if (NOT (MEMBER (CDR (ASSOC 0 (ENTGET EN))) '("ATTRIB" "VERTEX"
			  "SEQEND"
			 )
		 )
	    )
	  (PROGN
	    (SSADD EN SS)
	  )
	)
      )
      (if (ZEROP (SSLENGTH SS))
	(PROGN
	  (setq SS nil)
	)
      )
      SS
    )
    (PROGN
      (SSGET "_x")
    )
  )
)
(DEFUN MC:MD (PT1 PT2 / PTN)
  (setq PTN (MAPCAR
	      '(LAMBDA (X Y)
		 (/ (+ X Y) 2.0)
	       )
	      PT1
	      PT2
	    )
  )
)
(DEFUN SK_FFR (EN SK_RADIUS)
  (SETVAR "FILLETRAD" SK_RADIUS)
  (command "_.FILLET")
  (command "P")
  (command EN)
)
(vl-ACAD-defun (DEFUN C:MOYU_FPL_YY (/ SK_RADIUS1 SS EN)
		 (setq SK_RADIUS (GETVAR "FILLETRAD"))
		 (if (setq SK_RADIUS1 (GETDIST (STRCAT "\n请指定圆角半径<"
						       (RTOS SK_RADIUS) ">:"
					       )
				      )
		     )
		   (PROGN
		     (setq SK_RADIUS SK_RADIUS1)
		   )
		 )
		 (if (setq SS (SSGET '((0 . "*polyline"))))
		   (PROGN
		     (setq SK_ECHO1 (GETVAR "cmdecho"))
		     (SETVAR "cmdecho" 0)
		     (while (and
			      (setq EN (SSNAME SS 0))
			    )
		       (SK_FFR EN SK_RADIUS)
		       (setq SS (SSDEL EN SS))
		     )
		     (if SK_ECHO1
		       (PROGN
			 (SETVAR "cmdecho" SK_ECHO1)
		       )
		     )
		   )
		 )
		 (PRINC)
	       )
)
'C:MOYU_FPL_YY
(DEFUN PT3A (PT1 PT2 PT3)
  (if (setq CENT (INTERS
		   (POLAR PT1 (ANGLE PT1 PT2) (* 0.5 (DISTANCE PT1 PT2)))
		   (POLAR (POLAR PT1 (ANGLE PT1 PT2) (* 0.5
							(DISTANCE PT1 PT2)
						     )
			  ) (+ (* 0.5 PI) (ANGLE PT1 PT2)) 1.0
		   )
		   (POLAR PT3 (ANGLE PT3 PT2) (* 0.5 (DISTANCE PT3 PT2)))
		   (POLAR (POLAR PT3 (ANGLE PT3 PT2) (* 0.5
							(DISTANCE PT3 PT2)
						     )
			  ) (+ (* 0.5 PI) (ANGLE PT3 PT2)) 1.0
		   )
		   nil
		 )
      )
    (PROGN
      (ENTMAKE (LIST (CONS 0 "circle") (CONS 10 CENT) (CONS 40
							    (DISTANCE CENT
								      PT1
							    )
						      ) (CONS 62 102)
	       )
      )
    )
    (PROGN
      (ENTMAKE (LIST (CONS 0 "line") (CONS 10 PT1) (CONS 11 PT2)
		     (CONS 62 102)
	       )
      )
    )
  )
  (PRINC)
)
(vl-ACAD-defun (DEFUN C:MOYU_TRR_YY (/ PT1 PT2 S1 OSM_OLD ANG D PT3 PT4)
		 (SETVAR "CMDECHO" 0)
		 (setq OSM_OLD (GETVAR "OSMODE"))
		 (INITGET "N W")
		 (setq KXJQ (GETKWORD "\n框选剪切 [剪除框内(N)/剪除框外(W)]：<N>"))
		 (if (NOT KXJQ)
		   (PROGN
		     (setq KXJQ "N")
		   )
		 )
		 (COND
		   ((= KXJQ "N")
		     (while (and
			      (setq PT1 (GETPOINT "\n第一角点: "))
			    )
		       (if (setq PT2 (GETCORNER PT1 " >>>第二角点: "))
			 (PROGN
			   (SETVAR "OSMODE" 0)
			   (command "_rectang")
			   (command PT1)
			   (command PT2)
			   (setq S1 (ENTLAST))
			   (setq ANG (ANGLE PT1 PT2))
			   (setq D (DISTANCE PT1 PT2))
			   (setq D (* D 0.01))
			   (setq PT1 (POLAR PT1 ANG D))
			   (setq PT2 (POLAR PT2 (+ ANG PI) D))
			   (setq PT3 (LIST (CAR PT1) (CADR PT2)))
			   (setq PT4 (LIST (CAR PT2) (CADR PT1)))
			   (command "_.trim")
			   (command S1)
			   (command "")
			   (REPEAT 5
			     (command "f")
			     (command PT1)
			     (command PT3)
			     (command PT2)
			     (command PT4)
			     (command PT1)
			     (command "")
			   )
			   (command "")
			   (command "_.erase")
			   (command "w")
			   (command PT1)
			   (command PT2)
			   (command "")
			   (command "_.erase")
			   (command S1)
			   (command "")
			   (SETVAR "OSMODE" OSM_OLD)
			 )
		       )
		     )
		   )
		   ((= KXJQ "W")
		     (setq PT1 (GETPOINT "\n第一角点: "))
		     (if (setq PT2 (GETCORNER PT1 " >>>第二角点: "))
		       (PROGN
			 (SETVAR "OSMODE" 0)
			 (command "_rectang")
			 (command PT1)
			 (command PT2)
			 (setq S1 (ENTLAST))
			 (setq ANG (ANGLE PT1 PT2))
			 (setq D (DISTANCE PT1 PT2))
			 (setq D (* D 0.01))
			 (setq PT1 (POLAR PT1 (+ ANG PI) D))
			 (setq PT2 (POLAR PT2 ANG D))
			 (setq PT3 (LIST (CAR PT1) (CADR PT2)))
			 (setq PT4 (LIST (CAR PT2) (CADR PT1)))
			 (command "_.trim")
			 (command S1)
			 (command "")
			 (REPEAT 5
			   (command "f")
			   (command PT1)
			   (command PT3)
			   (command PT2)
			   (command PT4)
			   (command PT1)
			   (command "")
			 )
			 (command "")
			 (command "_.erase")
			 (command "all")
			 (command "r")
			 (command "w")
			 (command PT1)
			 (command PT2)
			 (command "")
			 (command "_.erase")
			 (command S1)
			 (command "")
			 (SETVAR "OSMODE" OSM_OLD)
		       )
		     )
		   )
		 )
		 (PRINC)
	       )
)
'C:MOYU_TRR_YY
(DEFUN DDF_ENTMAKEDIM (PT1 PT2)
  (COND
    ((OR
       (EQUAL 0 (ANGLE PT1 PT2) 0.001)
       (EQUAL PI (ANGLE PT1 PT2) 0.001)
     )
      (ENTMAKE (LIST '(0 . "DIMENSION") '(100 . "AcDbEntity") '(100 . "AcDbDimension")
		     (CONS 10 PT1) '(70 . 32) '(1 . "") '(100 . "AcDbAlignedDimension")
		     (CONS 13 PT1) (CONS 14 PT2) '(100 . "AcDbRotatedDimension")
	       )
      )
    )
    ((OR
       (EQUAL (/ PI 2) (ANGLE PT1 PT2) 0.001)
       (EQUAL (* PI 1.5) (ANGLE PT1 PT2) 0.001)
     )
      (ENTMAKE (LIST '(0 . "DIMENSION") '(100 . "AcDbEntity") '(100 . "AcDbDimension")
		     (CONS 10 PT1) '(70 . 33) '(1 . "") '(100 . "AcDbAlignedDimension")
		     (CONS 13 PT1) (CONS 14 PT2)
	       )
      )
    )
    ((AND
       (NULL (EQUAL 0 (ANGLE PT1 PT2) 0.001))
       (NULL (EQUAL (/ PI 2) (ANGLE PT1 PT2) 0.001))
     )
      (ENTMAKE (LIST '(0 . "DIMENSION") '(100 . "AcDbEntity") '(100 . "AcDbDimension")
		     (CONS 10 PT1) '(70 . 33) '(1 . "") '(100 . "AcDbAlignedDimension")
		     (CONS 13 PT1) (CONS 14 PT2)
	       )
      )
    )
  )
)
(PRIN1)
(DEFUN OSNAPPT (NAME PT / COLOR D H K LST NEARPT NEARPT2 OSMO PT1 PT2 PT3
		     PT4 PT5 PTX PTY X
	       )
  (if NAME
    (PROGN
      (ENTDEL NAME)
    )
  )
  (REDRAW)
  (if (< (GETVAR "osmode") 16384)
    (PROGN
      (setq COLOR (vla-get-AutoSnapMarkerColor (vla-get-Drafting
								 (vla-get-Preferences
										      (vlax-get-acad-object)
								 )
					       )
		  )
      )
      (setq H (/ (GETVAR "viewsize") (CADR (GETVAR "screensize"))))
      (setq D (GETVAR "pickbox"))
      (setq LST (LIST (* D H) (* (- D 0.5) H) (* (+ D 0.5) H)))
      (setq K (* 1.5 D H))
      (if (setq NEARPT (OSNAP PT "_END,_CEN,_NOD,_QUA,_INS,_TAN,_EXT"))
	(PROGN
	  (setq OSMO 1)
	)
      )
      (if (AND
	    (setq NEARPT2 (OSNAP PT "_NEA"))
	    (NOT (EQUAL NEARPT NEARPT2 K))
	  )
	(PROGN
	  (setq OSMO 2)
	  (setq NEARPT NEARPT2)
	)
      )
      (if (AND
	    (setq NEARPT2 (OSNAP PT "_MID"))
	    (EQUAL NEARPT NEARPT2 K)
	  )
	(PROGN
	  (setq OSMO 3)
	  (setq NEARPT NEARPT2)
	)
      )
      (if (AND
	    (setq NEARPT2 (OSNAP PT "_INT"))
	    (EQUAL NEARPT NEARPT2 K)
	  )
	(PROGN
	  (setq OSMO 4)
	  (setq NEARPT NEARPT2)
	)
      )
    )
  )
  (if NAME
    (PROGN
      (ENTDEL NAME)
    )
  )
  (if NEARPT
    (PROGN
      (setq PTX (CAR NEARPT))
      (setq PTY (CADR NEARPT))
      (FOREACH X LST
	(setq PT1 (LIST (- PTX X) (- PTY X)))
	(setq PT2 (LIST (+ PTX X) (- PTY X)))
	(setq PT3 (LIST (+ PTX X) (+ PTY X)))
	(setq PT4 (LIST (- PTX X) (+ PTY X)))
	(setq PT5 (LIST PTX (+ PTY X)))
	(COND
	  ((= OSMO 1)
	    (GRVECS (LIST COLOR PT1 PT2 PT2 PT3 PT3 PT4 PT4 PT1))
	  )
	  ((= OSMO 2)
	    (GRVECS (LIST COLOR PT1 PT2 PT2 PT4 PT3 PT4 PT3 PT1))
	  )
	  ((= OSMO 3)
	    (GRVECS (LIST COLOR PT1 PT2 PT2 PT5 PT5 PT1))
	  )
	  ((= OSMO 4)
	    (GRVECS (LIST COLOR PT1 PT3 COLOR PT2 PT4))
	  )
	)
      )
      (setq PT NEARPT)
    )
  )
  PT
)
(VL-LOAD-COM)
(DEFUN CENTSEL (MSG F)
  (while (and
	   (if (setq EL (CAR (ENTSEL MSG)))
	     (PROGN
	       (if (= (CDR (ASSOC 0 (ENTGET EL))) F)
		 (PROGN
		   nil
		 )
		 (PROGN
		   T
		 )
	       )
	     )
	   )
	 )
  )
  EL
)
(DEFUN DXF (X E)
  (CDR (ASSOC X E))
)
(DEFUN PTPER (PT0 PT1 PT2)
  (INTERS
    PT0
    (POLAR PT0 (+ (ANGLE PT1 PT2) (/ PI 2)) 1.0)
    PT1
    PT2
    nil
  )
)
(DEFUN LSORT (LT I)
  (COND
    ((OR
       (= I 0)
       (= I 2)
     )
      (setq LT (VL-SORT LT '(LAMBDA (E1 E2)
			      (< (CAR E1) (CAR E2))
			    )
	       )
      )
    )
    ((OR
       (= I 1)
       (= I 2)
     )
      (setq LT (VL-SORT LT '(LAMBDA (E1 E2)
			      (< (CADR E1) (CADR E2))
			    )
	       )
      )
    )
  )
)
(PRINC)
(DEFUN YAD_DIMAD1 (SS / YAD-DXF YAD-PERPT YAD-CHGENT N M ENT EN ANG W H
		      L_DAT L_MOV OLDANG MOV S PT PT1 PT2 L_ADJ EN L_EN
		      DISANG DISW DISH ITEM ITEM1
		  )
  (DEFUN YAD-DXF (EN N)
    (if (NOT (LISTP EN))
      (PROGN
	(setq EN (ENTGET EN))
      )
    )
    (CDR (ASSOC N EN))
  )
  (DEFUN YAD-PERPT (PT PT1 PT2)
    (INTERS
      PT1
      PT2
      PT
      (POLAR PT (+ (ANGLE PT1 PT2) (/ PI 2)) 1200)
      nil
    )
  )
  (DEFUN YAD-CHGENT (EN N / M VAL)
    (if (NOT (LISTP EN))
      (PROGN
	(setq EN (ENTGET EN))
      )
    )
    (FOREACH ITM N
      (setq M (CAR ITM))
      (setq VAL (CADR ITM))
      (if (ASSOC M EN)
	(PROGN
	  (setq EN (SUBST
		     (CONS M VAL)
		     (ASSOC M EN)
		     EN
		   )
	  )
	)
	(PROGN
	  (setq EN (APPEND
		     EN
		     (LIST (CONS M VAL))
		   )
	  )
	)
      )
    )
    (ENTMOD EN)
  )
  (if SS
    (PROGN
      (VL-CMDF "_.dimedit" "_h" SS "")
      (setq N -1)
      (setq M 0)
      (REPEAT (SSLENGTH SS)
	(setq ENT (SSNAME SS (setq N (1+ N))))
	(setq EN (YAD-DXF (TBLSEARCH "block" (YAD-DXF ENT 2)) -2))
	(while (and
		 (/= (YAD-DXF (setq EN (ENTNEXT EN))
			      0
		     ) "MTEXT"
		 )
	       )
	)
	(setq ANG (YAD-DXF EN 50))
	(setq H (YAD-DXF EN 43))
	(setq W (+ (/ (YAD-DXF EN 42) 2) (* 0.2 H)))
	(setq H (* 0.6 H))
	(setq L_DAT (CONS (LIST ENT ANG W H) L_DAT))
	(if (< (/ (YAD-DXF ENT 42) 2) W)
	  (PROGN
	    (if (= (REM (setq M (1+ M))
			2
		   ) 0
		)
	      (PROGN
		(setq L_MOV (CONS (LIST ENT ANG W H) L_MOV))
	      )
	      (PROGN
		(setq L_MOV (APPEND
			      L_MOV
			      (LIST (LIST ENT ANG W H))
			    )
		)
	      )
	    )
	  )
	)
      )
      (FOREACH ITM L_MOV
	(setq ENT (CAR ITM))
	(setq ANG (CADR ITM))
	(setq W (CADDR ITM))
	(setq H (CADDDR ITM))
	(setq PT (YAD-DXF ENT 11))
	(setq OLDANG (ANGLE PT (YAD-PERPT PT (setq PT1 (YAD-DXF ENT 10))
					  (POLAR PT1 ANG 1200)
			       )
		     )
	)
	(setq MOV T)
	(while (and
		 (AND
		   MOV
		   (setq S (SSGET "_f" (LIST (setq PT1 (POLAR
							      (POLAR PT ANG
								     W
							      ) (+ ANG
								   (/ PI 2)
								) H
						       )
					     )
					     (setq PT2 (POLAR PT1
							      (+ ANG PI)
							      (* 2 W)
						       )
					     )
					     (setq PT2 (POLAR PT2
							      (- ANG
								 (/ PI 2)
							      ) (* 2 H)
						       )
					     )
					     (POLAR PT2 ANG (* 2 W)) PT1
				       ) '((0 . "dimension") (-4 . "<or")
				   (70 . 0)
				   (70 . 1)
				   (70 . 32)
				   (70 . 33)
				   (70 . 128)
				   (70 . 129)
				   (70 . 160)
				   (70 . 161)
				   (-4 . "or>")
				  )
			   )
		   )
		 )
	       )
	  (setq N -1)
	  (setq L_ADJ nil)
	  (REPEAT (SSLENGTH S)
	    (setq EN (SSNAME S (setq N (1+ N))))
	    (if (AND
		  (SSMEMB EN SS)
		  (NOT (EQUAL EN ENT))
		  (setq L_EN (YAD-DXF L_DAT EN))
		  (EQUAL ANG (CAR L_EN) 0.01)
		)
	      (PROGN
		(setq PT1 (YAD-PERPT (YAD-DXF EN 11) PT (POLAR PT ANG 1200)))
		(setq DISANG (ANGLE PT1 PT))
		(setq DISW (- (+ W (CADR L_EN)) (DISTANCE PT PT1)))
		(setq DISH (- (+ H (CADDR L_EN)) (DISTANCE PT
							   (YAD-PERPT
								      (YAD-DXF EN 11)
								      PT
								      (POLAR PT
									     (+ ANG
										(/ PI 2)
									     ) 1200
								      )
							   )
						 )
			   )
		)
		(if (AND
		      (> DISH 0)
		      (NOT (EQUAL DISH 0 1))
		    )
		  (PROGN
		    (if (setq ITEM (VL-MEMBER-IF '(LAMBDA (E)
						    (EQUAL (CAR E) DISANG
							   0.01
						    )
						  ) L_ADJ
				   )
			)
		      (PROGN
			(setq ITEM (CAR ITEM))
			(setq L_ADJ (SUBST
				      (LIST DISANG (MAX
						     DISW
						     (CADR ITEM)
						   ) (MAX
						       DISH
						       (CADDR ITEM)
						     )
				      )
				      ITEM
				      L_ADJ
				    )
			)
		      )
		      (PROGN
			(setq L_ADJ (CONS (LIST DISANG DISW DISH) L_ADJ))
		      )
		    )
		  )
		)
	      )
	    )
	  )
	  (COND
	    ((NOT L_ADJ)
	      (setq MOV nil)
	    )
	    ((AND
	       (= (LENGTH L_ADJ) 1)
	       (setq ITEM (CAR L_ADJ))
	       (> (setq DISW (CADR ITEM))
		  0
	       )
	       (NOT (EQUAL DISW 0 1))
	       (> (CADDR ITEM) 0)
	     )
	      (if (> (YAD-DXF ENT 70) 128)
		(PROGN
		  (setq PT1 (YAD-PERPT PT (setq PT2 (YAD-DXF ENT 10))
				       (POLAR PT2 ANG 1200)
			    )
		  )
		  (YAD-CHGENT ENT (LIST (LIST 11 (setq PT (POLAR PT
								 (setq DISANG
								       (ANGLE PT PT1)
								 )
								 (* 2
								    (+
								       (DISTANCE PT PT1)
								       (if
									 (EQUAL DISANG OLDANG 0.01)
									 (PROGN
									   0
									 )
									 (PROGN
									   H
									 )
								       )
								    )
								 )
							  )
						 )
					) (LIST 70 (+ 128 (REM
							       (YAD-DXF ENT
									70
							       ) 128
							  )
						   )
					  )
				  )
		  )
		)
		(PROGN
		  (setq MOV nil)
		  (YAD-CHGENT ENT (LIST (LIST 11 (POLAR PT (CAR ITEM) DISW))
					(LIST 70 (+ 128 (REM
							     (YAD-DXF ENT 70)
							     128
							)
						 )
					)
				  )
		  )
		)
	      )
	    )
	    ((AND
	       (= (LENGTH L_ADJ) 2)
	       (setq ITEM (CAR L_ADJ))
	       (setq ITEM1 (CADR L_ADJ))
	       (OR
		 (AND
		   (> (setq DISW (CADR ITEM))
		      0
		   )
		   (NOT (EQUAL DISW 0 1))
		   (> (CADDR ITEM) 0)
		 )
		 (AND
		   (> (setq DISW (CADR ITEM1))
		      0
		   )
		   (NOT (EQUAL DISW 0 1))
		   (> (CADDR ITEM1) 0)
		 )
	       )
	     )
	      (if (OR
		    (> (YAD-DXF ENT 70) 128)
		    (AND
		      (> (CADDR ITEM) 0)
		      (> (CADDR ITEM1) 0)
		      (> (setq DISW (+ (CADR ITEM) (CADR ITEM1)))
			 0
		      )
		      (NOT (EQUAL DISW 0 1))
		    )
		  )
		(PROGN
		  (setq PT1 (YAD-PERPT PT (setq PT2 (YAD-DXF ENT 10))
				       (POLAR PT2 ANG 1200)
			    )
		  )
		  (if (EQUAL PT PT1 1)
		    (PROGN
		      (setq DISANG (- ANG (/ PI 2)))
		    )
		    (PROGN
		      (setq DISANG (ANGLE PT PT1))
		    )
		  )
		  (YAD-CHGENT ENT (LIST (LIST 11 (setq PT (POLAR PT DISANG
								 (* 2
								    (+
								       (DISTANCE PT PT1)
								       (if
									 (EQUAL DISANG OLDANG 0.01)
									 (PROGN
									   0
									 )
									 (PROGN
									   H
									 )
								       )
								    )
								 )
							  )
						 )
					) (LIST 70 (+ 128 (REM
							       (YAD-DXF ENT
									70
							       ) 128
							  )
						   )
					  )
				  )
		  )
		)
		(PROGN
		  (setq MOV nil)
		  (if (OR
			(< (CADDR ITEM) 0)
			(AND
			  (< (setq DISW (CADR ITEM))
			     0
			  )
			  (NOT (EQUAL DISW 0 1))
			)
		      )
		    (PROGN
		      (setq ITEM ITEM1)
		    )
		  )
		  (YAD-CHGENT ENT (LIST (LIST 11 (POLAR PT (CAR ITEM)
							(CADR ITEM)
						 )
					) (LIST 70 (+ 128 (REM
							       (YAD-DXF ENT
									70
							       ) 128
							  )
						   )
					  )
				  )
		  )
		)
	      )
	    )
	    (T
	      (setq MOV nil)
	    )
	  )
	)
      )
    )
  )
  (PRINC)
)
(DEFUN MC:QDD (SS / ANG0 ARC_CEN ARC_CEN3D ARC_STAP B DIST_OFFSET ENT_XNAME
		  ENTLI_X I I_SS L_ANGLE L_ENDP L_MIDP2D L_MIDP3D L_STAP
		  LIST_L OSM_OLD CLA_OLD PT3_ARC PT3_LINE REPEAT_NO
		  SS_AFTER_EXPLOD X Y
	      )
  (setq DIST_OFFSET (* 7 (GETVAR "dimscale")))
  (SETVAR "CMDECHO" 0)
  (YY_KAISHI_YY)
  (if (= (TBLOBJNAME "LAYER" "0-临时尺寸") nil)
    (PROGN
      (ENTMAKE (LIST '(0 . "LAYER") '(100 . "AcDbSymbolTableRecord") '
		     (100 . "AcDbLayerTableRecord") '(70 . 0) '(62 . 143)
		     (CONS 2 "0-临时尺寸")
	       )
      )
    )
  )
  (SETVAR "clayer" "0-临时尺寸")
  (SETVAR "osmode" 0)
  (GRAPHSCR)
  (setq I_SS 0)
  (setq III 0)
  (setq NS (SSLENGTH SS))
  (REPEAT NS
    (setq ASD (SSNAME SS III))
    (if (= (CDR (ASSOC 0 (ENTGET ASD))) "LWPOLYLINE")
      (PROGN
	(command "_.EXPLODE")
	(command ASD)
	(command "")
	(setq SS_AFTER_EXPLOD (SSGET "p"))
      )
      (PROGN
	(setq SS_AFTER_EXPLOD (SSADD))
	(SSADD ASD SS_AFTER_EXPLOD)
      )
    )
    (setq REPEAT_NO (SSLENGTH SS_AFTER_EXPLOD))
    (setq I 0)
    (REPEAT REPEAT_NO
      (setq ENT_XNAME (SSNAME SS_AFTER_EXPLOD I))
      (setq ENTLI_X (ENTGET ENT_XNAME))
      (if (= (CDR (ASSOC 0 ENTLI_X)) "ARC")
	(PROGN
	  (setq ARC_CEN3D (CDR (ASSOC 10 ENTLI_X)))
	  (setq ARC_CEN (LIST (CAR ARC_CEN3D) (CADR ARC_CEN3D)))
	  (setq PT3_ARC (POLAR ARC_CEN (+ (CDR (ASSOC 50 ENTLI_X)) 0.1)
			       (+ DIST_OFFSET (CDR (ASSOC 40 ENTLI_X)))
			)
	  )
	  (setq ARC_STAP (POLAR ARC_CEN (CDR (ASSOC 50 ENTLI_X))
				(CDR (ASSOC 40 ENTLI_X))
			 )
	  )
	  (command "_.DIMARC")
	  (command (LIST ENT_XNAME ARC_STAP))
	  (command PT3_ARC)
	)
	(PROGN
	  (setq L_STAP (CDR (ASSOC 10 ENTLI_X)))
	  (setq L_ENDP (CDR (ASSOC 11 ENTLI_X)))
	  (setq L_MIDP3D (MAPCAR
			   '(LAMBDA (X Y)
			      (* (+ X Y) 0.5)
			    )
			   L_STAP
			   L_ENDP
			 )
	  )
	  (setq L_MIDP2D (MAPCAR
			   '+
			   '(0 0)
			   L_MIDP3D
			 )
	  )
	  (setq L_ANGLE (ANGLE L_STAP L_ENDP))
	  (setq PT3_LINE (POLAR L_MIDP2D (+ L_ANGLE (DTR 90)) DIST_OFFSET))
	  (command "_.dimaligned")
	  (command "")
	  (command (LIST ENT_XNAME L_MIDP2D))
	  (command PT3_LINE)
	)
      )
      (setq I (1+ I))
    )
    (LJ SS_AFTER_EXPLOD)
    (setq I_SS (1+ I_SS))
    (setq III (1+ III))
  )
  (YY_END_YY)
  (PRINC)
)
(DEFUN DTR (ANG0)
  (* ANG0 (/ PI 180.0))
)
(DEFUN LJ (SSS / B)
  (SETVAR "peditaccept" 1)
  (setq B SSS)
  (command "pedit")
  (command "m")
  (command B)
  (command "")
  (command "j")
  (command "0")
  (command "")
  (SETVAR "peditaccept" 0)
  (PRIN1)
)

(defun c:MOYU_DSB_YY (/ ss ent idx ent1 filtered-ss)
    ;; 自定义函数，用于查找实体在选择集中的索引
    (defun my-ssmember (ent ss / idx)
        (setq idx 0)
        (while (and (setq ent1 (ssname ss idx))
                    (/= ent1 ent))
            (setq idx (1+ idx))
        )
        (if (equal ent1 ent)
            idx
            nil
        )
    )

    ;; 检查是否已经有选择集
    (if (setq ss (ssget))
        ;; 如果已经有选择集，直接使用
        (progn)
        ;; 如果没有选择集，提示用户框选区域
        (progn
            (prompt "\n请框选要删除标注的区域：")
            (setq ss (ssget))
        )
    )

    ;; 如果成功获取到选择集
    (if ss
        (progn
            ;; 筛选出选择集中的标注对象
            (setq filtered-ss (ssadd))
            (setq idx 0)
            (while (setq ent (ssname ss idx))
                (setq ent-type (cdr (assoc 0 (entget ent))))
                ;; 检查所有类型的标注
                (if (or (equal ent-type "DIMENSION")    ; 普通标注
                       (equal ent-type "LEADER")        ; 引线标注
                       (equal ent-type "MULTILEADER")   ; 多重引线标注
                       (equal ent-type "ARCALIGNEDDIMENSION") ; 圆弧标注
                       (equal ent-type "RADIALDIMENSION")     ; 半径标注
                       (equal ent-type "DIAMETRICDIMENSION")  ; 直径标注
                       (wcmatch ent-type "*DIMARC")    ; 弧长标注
                       (wcmatch ent-type "*DIMRADIUS") ; 半径标注
                       (wcmatch ent-type "*DIMDIA"))   ; 直径标注
                    (ssadd ent filtered-ss)
                )
                (setq idx (1+ idx))
            )
            ;; 遍历筛选后的选择集中的每个标注对象
            (setq idx 0)
            (repeat (sslength filtered-ss)
                (setq ent (ssname filtered-ss idx))
                (if ent
                    (entdel ent)
                )
                (setq idx (1+ idx))
            )
            (princ "\n框选区域内的所有标注已删除。")
        )
        (princ "\n未选择任何区域或未找到标注对象。")
    )
    (princ)
)
'C:MOYU_DSB_YY
(DEFUN JCTC_Z ()
  (SETVAR "cmdecho" 0)
  (DEFUN *ERROR* (MSG)
    (if (AND
	  MSG
	  (NOT (WCMATCH (STRCASE MSG) "*BREAK*,*CANCEL*,*QUIT*,*EXIT*,"))
	)
      (PROGN
	(PRINC)
      )
    )
  )
  (SETVAR "measurement" 1)
  (SETVAR "measureinit" 1)
  (PRINC "\n请选择填充区域：右键改为点选区域）")
  (if (setq SS (SSGET))
    (PROGN
      (command "bhatch")
      (command "s")
      (command SS)
      (command "")
      (command "")
    )
    (PROGN
      (PRINC "\n请拾取填充内部点：")
      (command "bhatch")
      (command PAUSE)
      (command PAUSE)
    )
  )
  (command "_.undo")
  (command "_group")
  (command "change")
  (command (ENTLAST))
  (command "")
  (command "P")
  (command "la")
  (command TC_NAME)
  (command "")
  (command "change")
  (command (ENTLAST))
  (command "")
  (command "P")
  (command "c")
  (command "bylayer")
  (command "")
  (command "_.undo")
  (command "_end")
  (PRINC)
)
(vl-ACAD-defun (DEFUN C:MOYU_FFX_YY (/ ENT)
		 (PROMPT "\n更改线型为虚线")
		 (SETVAR "CMDECHO" 0)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "lt")
		 (command "dashed")
		 (command "")
		 (SETVAR "CMDECHO" 1)
		 (PRINC)
	       )
)
'C:MOYU_FFX_YY
(DEFUN EF:UNDOBEGIN ()
  (SETVAR "CMDECHO" 0)
  (command "_.undo")
  (command "_group")
  (PRINC)
)
(DEFUN EF:UNDOEND ()
  (SETVAR "CMDECHO" 0)
  (command "_.undo")
  (command "_end")
  (PRINC)
)
(vl-ACAD-defun (DEFUN C:MOYU_TT_YY (/ DCL_ID1 OBA OB1 OBN OBT PTN OTXT TXT
				     STY STYNO LAY CYN LAYNO HIG WID ANG COL
				     CNU ETLST STYLE LAYER
				  )
		 (GRAPHSCR)
		 (EF:UNDOBEGIN)
		 (setq OLDERR *ERROR*)
		 (DEFUN *ERROR* (MSG)
		   (PRINC "\n*ERROR*...")
		   (PRINC MSG)
		   (PRINC)
		 )
		 (DEFUN SET_COLOR (CONM / COSTR)
		   (DEFUN MAP_COLOR (CKEY MNO)
		     (START_IMAGE CKEY)
		     (FILL_IMAGE 0 0 (DIMX_TILE CKEY) (DIMY_TILE CKEY) MNO)
		     (END_IMAGE)
		   )
		   (COND
		     ((= 0 CONM)
		       (setq COSTR "Byblock")
		     )
		     ((= 1 CONM)
		       (setq COSTR "Red")
		     )
		     ((= 2 CONM)
		       (setq COSTR "Yellow")
		     )
		     ((= 3 CONM)
		       (setq COSTR "Green")
		     )
		     ((= 4 CONM)
		       (setq COSTR "Cyan")
		     )
		     ((= 5 CONM)
		       (setq COSTR "Bule")
		     )
		     ((= 6 CONM)
		       (setq COSTR "Magenta")
		     )
		     ((= 7 CONM)
		       (setq COSTR "color")
		     )
		     ((= 256 CONM)
		       (setq COSTR "Bylayer")
		     )
		     (T
		       (setq COSTR "")
		     )
		   )
		   (COND
		     ((= 0 COL)
		       (MAP_COLOR "col" 7)
		     )
		     ((= 256 COL)
		       (MAP_COLOR "col" (CDR (ASSOC 62 (TBLSEARCH "layer"
								  LAY
						       )
					     )
					)
		       )
		     )
		     (T
		       (MAP_COLOR "col" CONM)
		     )
		   )
		   (if (= 256 CONM)
		     (PROGN
		       (SET_TILE "cnu" (STRCAT "<" (ITOA (CDR
							      (ASSOC 62
								     (TBLSEARCH "layer" LAY)
							      )
							 )
						   ) ">" COSTR
				       )
		       )
		     )
		     (PROGN
		       (SET_TILE "cnu" (STRCAT "<" (ITOA CONM) ">" COSTR))
		     )
		   )
		 )
		 (DEFUN MAP_KEYLIST (KEY KEYLST)
		   (START_LIST KEY)
		   (MAPCAR
		     'ADD_LIST
		     KEYLST
		   )
		   (END_LIST)
		 )
		 (DEFUN LAYER_GET_ALL (/ LAY LAYER LAYNAME)
		   (setq LAYER nil)
		   (setq LAY (TBLNEXT "LAYER" T))
		   (while (and
			    (/= LAY nil)
			  )
		     (setq LAYNAME (CDR (ASSOC 2 LAY)))
		     (setq LAYER (CONS LAYNAME LAYER))
		     (setq LAY (TBLNEXT "LAYER"))
		   )
		   (setq LAYER (ACAD_STRLSORT LAYER))
		   LAYER
		 )
		 (DEFUN STYLE_GET_ALL (/ STY STYLE STY_LIST)
		   (setq STY_LIST nil)
		   (setq STY (TBLNEXT "style" T))
		   (setq STYLE (CDR (ASSOC 2 STY)))
		   (while (and
			    STYLE
			  )
		     (if (/= "" STYLE)
		       (PROGN
			 (setq STY_LIST (APPEND
					  STY_LIST
					  (LIST STYLE)
					)
			 )
		       )
		     )
		     (setq STY (TBLNEXT "style"))
		     (setq STYLE (CDR (ASSOC 2 STY)))
		   )
		   (setq STY_LIST (ACAD_STRLSORT STY_LIST))
		   STY_LIST
		 )
		 (DEFUN SET_ERROR (STR)
		   (SET_TILE "error" STR)
		 )
		 (DEFUN SUB_MTEXT (COLOR ENTLIST / EI NEWLIST)
		   (setq EI 0)
		   (setq NEWLIST nil)
		   (while (and
			    (< EI (LENGTH ENTLIST))
			  )
		     (setq NEWLIST (CONS (NTH EI ENTLIST) NEWLIST))
		     (if (= 8 (CAR (NTH EI ENTLIST)))
		       (PROGN
			 (setq NEWLIST (CONS (CONS 62 COLOR) NEWLIST))
		       )
		     )
		     (setq EI (1+ EI))
		   )
		   (REVERSE NEWLIST)
		 )
		 (setq OB1 (ENTSEL "\n选择要修改的任何文本:"))
		 (setq OBN (CAR OB1))
		 (setq PTN (CAR (CDR OB1)))
		 (setq OBT (CAR (NENTSELP PTN)))
		 (setq OBA (CDR (ASSOC 0 (ENTGET OBT))))
		 (if (OR
		       (= OBA "TEXT")
		       (= OBA "MTEXT")
		       (= OBA "ATTRIB")
		     )
		   (PROGN
		     (setq OTXT (CDR (ASSOC 1 (ENTGET OBT))))
		   )
		 )
		 (if (= OBA "ATTDEF")
		   (PROGN
		     (setq OTXT (CDR (ASSOC 2 (ENTGET OBT))))
		   )
		 )
		 (if OTXT
		   (PROGN
		     (setq STY (CDR (ASSOC 7 (ENTGET OBT))))
		     (setq LAY (CDR (ASSOC 8 (ENTGET OBN))))
		     (setq HIG (CDR (ASSOC 40 (ENTGET OBT))))
		     (setq WID (CDR (ASSOC 41 (ENTGET OBT))))
		     (setq ANG (CDR (ASSOC 50 (ENTGET OBT))))
		     (if (OR
			   (= OBA "TEXT")
			   (= OBA "MTEXT")
			   (= OBA "ATTRIB")
			 )
		       (PROGN
			 (setq COL (CDR (ASSOC 62 (ENTGET OBT))))
		       )
		       (PROGN
			 (setq COL (CDR (ASSOC 62 (ENTGET OBN))))
		       )
		     )
		     (setq ANG (* 180 (/ ANG PI)))
		     (if (NULL COL)
		       (PROGN
			 (setq CYN 0)
			 (setq COL 256)
		       )
		       (PROGN
			 (setq CYN 1)
		       )
		     )
		     (setq STYLE (STYLE_GET_ALL))
		     (setq LAYER (LAYER_GET_ALL))
		     (setq STYNO (- (LENGTH STYLE) (LENGTH (MEMBER STY STYLE))))
		     (setq LAYNO (- (LENGTH LAYER) (LENGTH (MEMBER LAY LAYER))))
		     (setq TEMPNAME (VL-FILENAME-MKTEMP "tt-dcl-tmp.dcl"))
		     (setq DCLNAME (COND
				     ((setq FILEN (OPEN TEMPNAME "w"))
				       (FOREACH STREAM '("\n"
					  "文字修改:dialog {\n"
					  "\tlabel = \"文字编辑...\";\n"
					  "\t: boxed_radio_column {\n"
					  "\t\tlabel = \"超级文字编辑...\";\n"
					  "\t\t: edit_box {\n"
					  "\t\t\tlabel= \"文字:\";\n"
					  "\t\t\tkey = \"text\";\n"
					  "\t\t\tedit_width = 50;\n" "                                             allow_accept = true;\n"
					  "\t\t}\n" "\t\t: row {\n"
					  "\t\t\t: popup_list {\n"
					  "\t\t\t\tlabel=\"样式\";\n"
					  "\t\t\t\tkey = \"sty\";\n"
					  "\t\t\t\tedit_width = 13;\n"
					  "\t\t\t\tfixed_width = true;\n"
					  "\t\t\t}\n"
					  "\t\t\t: edit_box {\n"
					  "\t\t\t\tlabel=\"高度\";\n"
					  "\t\t\t\tkey = \"hig\";\n"
					  "\t\t\t\tedit_width = 7;\n"
					  "\t\t\t\tfixed_width = true;\n"
					  "\t\t\t}\n"
					  "\t\t\t: edit_box {\n"
					  "\t\t\t\tlabel=\"宽度\";\n"
					  "\t\t\t\tkey = \"wid\";\n"
					  "\t\t\t\tedit_width = 7;\n"
					  "\t\t\t\tfixed_width = true;\n"
					  "\t\t\t}\n"
					  "\t\t}\n" "\t\t: row {\n"
					  "\t\t\t: popup_list {\n"
					  "\t\t\t\tlabel=\"图层\";\n"
					  "\t\t\t\tkey = \"lay\";\n"
					  "\t\t\t\tedit_width = 13;\n"
					  "\t\t\t\tfixed_width = true;\n"
					  "\t\t\t}\n"
					  "\t\t\t: image_button {\n"
					  "\t\t\t\tkey = \"col\";\n"
					  "\t\t\t\twidth= 4;\n"
					  "\t\t\t\taspect_ratio = 0.75;\n"
					  "\t\t\t\tfixed_width = true;\n"
					  "\t\t\t}\n"
					  "\t\t\t: text_part {\n"
					  "\t\t\t\tkey = \"cnu\";\n"
					  "\t\t\t\twidth= 12;\n"
					  "\t\t\t\tfixed_width = true;\n"
					  "\t\t\t}\n" "\t\t\t: edit_box {\n"
					  "\t\t\t\tlabel=\"角度\";\n"
					  "\t\t\t\tkey = \"ang\";\n"
					  "\t\t\t\tedit_width = 7;\n"
					  "\t\t\t\tfixed_width = true;\n"
					  "\t\t\t}\n" "\t\t}\n"
					  "\t\tspacer_1;\n" "\t}\n"
					  "\t: row {\n"
					  "\t\talignment = right;\n"
					  "\t\t: spacer {\n"
					  "\t\t\twidth = 1;\n"
					  "\t\t\tfixed_width = true;\n"
					  "\t\t\t}\n"
					  "\t\tok_cancel;\n" "\t}\n"
					  "\terrtile;\n" "}\n"
					 )
					 (PRINC STREAM FILEN)
				       )
				       (CLOSE FILEN)
				       TEMPNAME
				     )
				   )
		     )
		     (setq DCL_ID1 (LOAD_DIALOG DCLNAME))
		     (if (NOT (NEW_DIALOG "文字修改" DCL_ID1))
		       (PROGN
			 (EXIT)
		       )
		     )
		     (SET_COLOR COL)
		     (SET_TILE "text" OTXT)
		     (SET_TILE "hig" (RTOS HIG 2 2))
		     (SET_TILE "wid" (RTOS WID 2 2))
		     (SET_TILE "ang" (RTOS ANG 2 2))
		     (MODE_TILE "text" 2)
		     (MAP_KEYLIST "sty" STYLE)
		     (SET_TILE "sty" (ITOA STYNO))
		     (MAP_KEYLIST "lay" LAYER)
		     (SET_TILE "lay" (ITOA LAYNO))
		     (ACTION_TILE "text" "(setq txt $value)")
		     (ACTION_TILE "sty" "(setq styno (atoi $value))")
		     (ACTION_TILE "hig" "(setq hig (distof $value))(if (>= 0 hig)(progn (mode_tile \"hig\" 3)(mode_tile \"hig\" 2)(set_error \"Input error ! \"))(set_error \"\"))")
		     (ACTION_TILE "wid" "(setq wid (distof $value))(if (>= 0 wid)(progn (mode_tile \"wid\" 3)(mode_tile \"wid\" 2)(set_error \"Input error ! \"))(set_error \"\"))")
		     (ACTION_TILE "lay" "(setq layno (atoi $value))")
		     (ACTION_TILE "col" "(if (setq cnu (ACAD_ColorDlg col))(progn (setq col cnu)(set_color col)))")
		     (ACTION_TILE "ang" "(setq ang (distof $value))")
		     (ACTION_TILE "accept" "(done_dialog 1)")
		     (ACTION_TILE "cancel" "(done_dialog 0)")
		     (if (= 1 (START_DIALOG))
		       (PROGN
			 (if TXT
			   (PROGN
			     (setq STY (NTH STYNO STYLE))
			     (setq LAY (NTH LAYNO LAYER))
			     (setq ANG (* (/ ANG 180) PI))
			     (setq ETLST (ENTGET OBT))
			     (if (= OBA "ATTDEF")
			       (PROGN
				 (setq ETLST (SUBST
					       (CONS 2 TXT)
					       (ASSOC 2 ETLST)
					       ETLST
					     )
				 )
			       )
			       (PROGN
				 (setq ETLST (SUBST
					       (CONS 1 TXT)
					       (ASSOC 1 ETLST)
					       ETLST
					     )
				 )
			       )
			     )
			     (setq ETLST (SUBST
					   (CONS 7 STY)
					   (ASSOC 7 ETLST)
					   ETLST
					 )
			     )
			     (setq ETLST (SUBST
					   (CONS 40 HIG)
					   (ASSOC 40 ETLST)
					   ETLST
					 )
			     )
			     (setq ETLST (SUBST
					   (CONS 41 WID)
					   (ASSOC 41 ETLST)
					   ETLST
					 )
			     )
			     (setq ETLST (SUBST
					   (CONS 50 ANG)
					   (ASSOC 50 ETLST)
					   ETLST
					 )
			     )
			     (setq ETLST (SUBST
					   (CONS 8 LAY)
					   (ASSOC 8 ETLST)
					   ETLST
					 )
			     )
			     (if (= 1 CYN)
			       (PROGN
				 (setq ETLST (SUBST
					       (CONS 62 COL)
					       (ASSOC 62 ETLST)
					       ETLST
					     )
				 )
			       )
			       (PROGN
				 (if (= "MTEXT" OBA)
				   (PROGN
				     (setq ETLST (SUB_MTEXT COL ETLST))
				   )
				   (PROGN
				     (setq ETLST (CONS (CONS 62 COL) ETLST))
				   )
				 )
			       )
			     )
			     (ENTMOD ETLST)
			     (ENTUPD OBT)
			     (ENTUPD OBN)
			   )
			 )
		       )
		     )
		     (if (= 11 (START_DIALOG))
		       (PROGN
			 (command "_help")
		       )
		     )
		   )
		 )
		 (setq *ERROR* OLDERR)
		 (EF:UNDOEND)
		 (PRINC)
		 (UNLOAD_DIALOG DCL_ID1)
		 (VL-FILE-DELETE DCLNAME)
	       )
)
'C:MOYU_TT_YY
(vl-ACAD-defun (DEFUN C:MOYU_TZK_YY (/ EDATA EI ENT N NEWWID NEWWIDSTR SI SS
				      STR TPY WIDSTR
				   )
		 (if (setq SS (SSGET '((0 . "*TEXT"))))
		   (PROGN
		     (if (= nil (setq NEWWID (GETREAL "\n 请输入新的文字宽度因子，默认<1.0>：")))
		       (PROGN
			 (setq NEWWID 1.0)
		       )
		     )
		     (if (> NEWWID 10)
		       (PROGN
			 (setq NEWWID 10.0)
		       )
		     )
		     (setq N -1)
		     (while (and
			      (setq ENT (SSNAME SS (setq N (1+ N))))
			    )
		       (setq EDATA (ENTGET ENT))
		       (setq TPY (CDR (ASSOC 0 EDATA)))
		       (if (EQUAL TPY "TEXT")
			 (PROGN
			   (ENTMOD (SUBST
				     (CONS 41 NEWWID)
				     (ASSOC 41 EDATA)
				     EDATA
				   )
			   )
			 )
			 (PROGN
			   (setq STR (CDR (ASSOC 1 EDATA)))
			   (if (WCMATCH STR "*`\\W##;*,*`\\W#;*,*`\\W#.#;*,*`\\W#.##;*,*`\\W#.###;*,*`\\W#.####;*")
			     (PROGN
			       (setq SI (1+ (VL-STRING-SEARCH "\\W" STR)))
			       (setq EI (1+ (VL-STRING-SEARCH ";" STR
							      (1+ SI)
					    )
					)
			       )
			       (setq WIDSTR (SUBSTR STR SI (1+ (- EI SI))))
			       (setq NEWWIDSTR (STRCAT "\\W" (RTOS NEWWID 2
								   2
							     ) ";"
					       )
			       )
			       (setq STR (VL-STRING-SUBST NEWWIDSTR WIDSTR
							  STR
					 )
			       )
			       (ENTMOD (SUBST
					 (CONS 1 STR)
					 (ASSOC 1 EDATA)
					 EDATA
				       )
			       )
			     )
			     (PROGN
			       (if (WCMATCH STR "{*}")
				 (PROGN
				   (setq NEWWIDSTR (STRCAT "{\\W"
							   (RTOS NEWWID 2 2)
							   ";"
						   )
				   )
				   (setq STR (VL-STRING-SUBST NEWWIDSTR "{"
							      STR
					     )
				   )
				   (ENTMOD (SUBST
					     (CONS 1 STR)
					     (ASSOC 1 EDATA)
					     EDATA
					   )
				   )
				 )
				 (PROGN
				   (setq STR (STRCAT "{\\W" (RTOS NEWWID 2 2)
						     ";" STR "}"
					     )
				   )
				   (ENTMOD (SUBST
					     (CONS 1 STR)
					     (ASSOC 1 EDATA)
					     EDATA
					   )
				   )
				 )
			       )
			     )
			   )
			 )
		       )
		     )
		   )
		   (PROGN
		     (ALERT "请选择文字对象后再行尝试！")
		   )
		 )
		 (PRIN1)
	       )
)
'C:MOYU_TZK_YY
(defun c:MOYU_TTH_YY (/ dcl_id fn old new ss)
  ;; 内部函数：创建对话框DCL文件
  (defun write_dcl_file ()
    (setq fn (strcat (getenv "TEMP") "\\bth.dcl"))
    (setq fp (open fn "w"))
    (write-line "bth : dialog {" fp)
    (write-line "  label = \"文字替换\";" fp)
    (write-line "  : column {" fp)
    (write-line "    : edit_box {" fp)
    (write-line "      label = \"查找内容:\";" fp)
    (write-line "      key = \"old\";" fp)
    (write-line "      width = 40;" fp)
    (write-line "      value = \"\";" fp)
    (write-line "    }" fp)
    (write-line "    : edit_box {" fp)
    (write-line "      label = \"替换为:\";" fp)
    (write-line "      key = \"new\";" fp)
    (write-line "      width = 40;" fp)
    (write-line "      value = \"\";" fp)
    (write-line "    }" fp)
    (write-line "    : row {" fp)
    (write-line "      : button {" fp)
    (write-line "        label = \"替换\";" fp)
    (write-line "        key = \"accept\";" fp)
    (write-line "        is_default = true;" fp)
    (write-line "      }" fp)
    (write-line "      : button {" fp)
    (write-line "        label = \"取消\";" fp)
    (write-line "        key = \"cancel\";" fp)
    (write-line "      }" fp)
    (write-line "    }" fp)
    (write-line "  }" fp)
    (write-line "}" fp)
    (close fp)
    fn
  )

  ;; 内部函数：替换文字
  (defun replace_text (ent old new / entdata txt)
    (setq entdata (entget ent))
    (setq etype (cdr (assoc 0 entdata)))
    (cond 
      ;; 处理单行文字
      ((= etype "TEXT")
       (setq txt (cdr (assoc 1 entdata)))
       (if (and txt (vl-string-search old txt))
         (progn
           (setq txt (vl-string-subst new old txt))
           (entmod (subst (cons 1 txt) (assoc 1 entdata) entdata))
           1
         )
         0
       )
      )
      ;; 处理多行文字
      ((= etype "MTEXT")
       (setq txt (cdr (assoc 1 entdata)))
       (if (and txt (vl-string-search old txt))
         (progn
           (setq txt (vl-string-subst new old txt))
           (entmod (subst (cons 1 txt) (assoc 1 entdata) entdata))
           1
         )
         0
       )
      )
      ;; 处理属性文字
      ((= etype "ATTRIB")
       (setq txt (cdr (assoc 1 entdata)))
       (if (and txt (vl-string-search old txt))
         (progn
           (setq txt (vl-string-subst new old txt))
           (entmod (subst (cons 1 txt) (assoc 1 entdata) entdata))
           1
         )
         0
       )
      )
      ;; 处理块参照（可能包含属性）
      ((= etype "INSERT")
       (setq count 0)
       (setq att (entnext ent))
       (while (and att (= "ATTRIB" (cdr (assoc 0 (entget att)))))
         (setq count (+ count (replace_text att old new)))
         (setq att (entnext att))
       )
       count
      )
      (t 0)
    )
  )

  ;; 内部函数：处理选择集
  (defun process_selection (old new ss / count ent i)
    (setq count 0
          i 0)
    (if ss
      (progn
        (repeat (sslength ss)
          (setq ent (ssname ss i))
          (setq count (+ count (replace_text ent old new)))
          (setq i (1+ i))
        )
        (if (> count 0)
          (alert (strcat "替换完成！\n共替换了 " (itoa count) " 处文字。"))
          (alert "未找到可替换的文字。")
        )
      )
    )
  )

  ;; 主程序开始
  ;; 检查是否有预选的对象
  (setq ss (ssget "_I"))
  
  ;; 如果没有预选对象，提示用户选择
  (if (not ss)
    (progn
      (prompt "\n选择要替换的文字对象: ")
      (setq ss (ssget))
    )
  )
  
  ;; 如果有选择的对象，继续处理
  (if ss
    (progn
      ;; 创建并显示对话框
      (setq fn (write_dcl_file))
      (setq dcl_id (load_dialog fn))
      
      (if (not (new_dialog "bth" dcl_id))
        (progn
          (alert "无法创建对话框!")
          (exit)
        )
      )
      
      ;; 设置初始值
      (set_tile "old" "")
      (set_tile "new" "")
      
      ;; 动作处理
      (action_tile "accept" "(setq user-old (get_tile \"old\") user-new (get_tile \"new\")) (done_dialog 1)")
      (action_tile "cancel" "(done_dialog 0)")
      
      ;; 显示对话框并获取结果
      (setq result (start_dialog))
      (unload_dialog dcl_id)
      (vl-file-delete fn)
      
      ;; 处理对话框结果
      (if (= result 1)
        (if (and user-old (/= user-old ""))
          (process_selection user-old user-new ss)
          (alert "请输入要查找的内容！")
        )
      )
    )
  )
  (princ)
)
'C:MOYU_TTH_YY
(vl-ACAD-defun (DEFUN C:MOYU_TZD_YY (/ *ERROR* CMDE DOC TSS INC TENT TOBJ
				      TINS TJUST
				   )
		 (PROMPT "【单行文字改为多行文字】")
		 (DEFUN *ERROR* (ERRMSG)
		   (if (NOT (WCMATCH ERRMSG "Function cancelled,quit / exit abort,console break"))
		     (PROGN
		       (PRINC (STRCAT "\nError: " ERRMSG))
		     )
		   )
		   (vla-EndUndoMark DOC)
		   (SETVAR 'CMDECHO CMDE)
		   (PRINC)
		 )
		 (setq CMDE (GETVAR 'CMDECHO))
		 (setq DOC (vla-get-ActiveDocument
						   (vlax-get-acad-object)
			   )
		 )
		 (vla-StartUndoMark DOC)
		 (SETVAR 'CMDECHO 0)
		 (if (setq TSS (SSGET "_:L" '((0 . "TEXT"))))
		   (PROGN
		     (REPEAT (setq INC (SSLENGTH TSS))
		       (setq TENT (SSNAME TSS (setq INC (1- INC))))
		       (setq TOBJ (vlax-ename->vla-object TENT))
		       (setq TINS (vlax-get TOBJ 'TEXTALIGNMENTPOINT))
		       (setq TJUST (vla-get-Alignment TOBJ))
		       (COND
			 ((= TJUST 0)
			   (setq TJUST 7)
			   (setq TINS (vlax-get TOBJ 'INSERTIONPOINT))
			 )
			 ((< TJUST 3)
			   (setq TJUST (+ TJUST 7))
			 )
			 ((= TJUST 4)
			   (setq TJUST 5)
			 )
			 ((MEMBER TJUST '(3 5))
			   (setq TJUST 8)
			   (setq TINS (MAPCAR
					'/
					(MAPCAR
					  '+
					  (vlax-get TOBJ 'INSERTIONPOINT)
					  TINS
					)
					'(2 2 2)
				      )
			   )
			 )
			 ((setq TJUST (- TJUST 5)))
		       )
		       (if (= (vla-get-TextString TOBJ) "")
			 (PROGN
			   (vla-put-TextString TOBJ
					       (vla-get-TagString TOBJ)
			   )
			 )
		       )
		       (command "_.txt2mtxt")
		       (command TENT)
		       (command "")
		       (setq TOBJ (vlax-ename->vla-object (ENTLAST)))
		       (vla-put-AttachmentPoint TOBJ TJUST)
		       (vlax-put TOBJ 'INSERTIONPOINT TINS)
		     )
		   )
		 )
		 (SETVAR 'CMDECHO CMDE)
		 (vla-EndUndoMark DOC)
		 (PRINC "\n单行文字已改为多行文字\n")
		 (PRINC)
	       )
)
'C:MOYU_TZD_YY
(DEFUN STRTYPE (A / B C D E)
  (setq B (VL-STRING->LIST A))
  (while (and
	   B
	 )
    (setq A (CAR B))
    (setq B (CDR B))
    (setq C (LAST D))
    (if (OR
	  (NOT D)
	  (AND
	    (< 0 A 32)
	    (< 0 C 32)
	  )
	  (OR
	    (= 46 A)
	    (= 46 C)
	    (AND
	      (< 47 A 58)
	      (< 47 C 58)
	    )
	  )
	  (VL-EVERY '(LAMBDA (X)
		       (VL-SOME (QUOTE (LAMBDA (Y)
					 (< (CAR Y) X (CADR Y))
				       )
				) (QUOTE ((31 48) (57 65)
					  (90 98)
					  (122 129)
					 )
				  )
		       )
		     ) (LIST A C)
	  )
	  (VL-EVERY '(LAMBDA (X)
		       (VL-SOME (QUOTE (LAMBDA (Y)
					 (< (CAR Y) X (CADR Y))
				       )
				) (QUOTE ((64 91) (96 123)))
		       )
		     ) (LIST A C)
	  )
	  (AND
	    (> A 128)
	    (> C 128)
	  )
	)
      (PROGN
	(if (> A 128)
	  (PROGN
	    (setq D (VL-LIST* (CAR B) A D))
	    (setq B (CDR B))
	  )
	  (PROGN
	    (setq D (CONS A D))
	  )
	)
      )
      (PROGN
	(setq E (CONS (REVERSE D) E))
	(setq D (if (> A 128)
		  (PROGN
		    (LIST (CAR B) A)
		  )
		  (PROGN
		    (LIST A)
		  )
		)
	)
	(setq B (if (> A 128)
		  (PROGN
		    (CDR B)
		  )
		  (PROGN
		    B
		  )
		)
	)
      )
    )
  )
  (MAPCAR
    'VL-LIST->STRING
    (REVERSE (CONS (REVERSE D) E))
  )
)
(DEFUN GET_FILTER (/ TMP POP TXT TXT1 RC TXT2 TXT3 POP_1 POP_2 POP_3)
  (COND
    ((= "1" (GET_TILE "hand"))
      (setq HAND "1")
    )
    ((= "1" (GET_TILE "pre"))
      (setq HAND "2")
    )
    ((= "1" (GET_TILE "all"))
      (setq HAND "3")
    )
  )
  (FOREACH TMP KLST
    (if (/= "1" (GET_TILE (CAR TMP)))
      (PROGN
	(setq KLST (VL-REMOVE TMP KLST))
      )
    )
  )
  (FOREACH TMP FJLST
    (if (/= "1" (GET_TILE (CAR TMP)))
      (PROGN
	(setq FJLST (VL-REMOVE TMP FJLST))
      )
    )
  )
  (setq KL_PRE (APPEND
		 (LIST (DXF 0 ENTL))
		 KLST
		 FJLST
	       )
  )
  (FOREACH TMP KLST
    (setq POP (GET_TILE (CADR TMP)))
    (COND
      ((MEMBER (CAR TMP) '("0" "1"
		"2" "3"
		"6" "7"
		"8"
	       )
       )
	(setq TXT (GET_TILE (CAADDR TMP)))
	(setq TXT1 (CADR (CADDR TMP)))
	(if (= TXT TXT1)
	  (PROGN
	    (setq TXT (DXF (READ (CAR TMP)) ENTL))
	  )
	)
	(COND
	  ((= POP "0")
	    (setq FILTER (APPEND
			   (CONS '(-4 . "<OR") (CONS (CONS (READ
								 (CAR TMP)
							   ) TXT
						     ) '((-4 . "OR>"))
					       )
			   )
			   FILTER
			 )
	    )
	  )
	  ((= POP "1")
	    (setq FILTER (APPEND
			   (CONS '(-4 . "<NOT") (CONS (CONS (READ
								  (CAR TMP)
							    ) TXT
						      ) '((-4 . "NOT>"))
						)
			   )
			   FILTER
			 )
	    )
	  )
	)
      )
      ((MEMBER (CAR TMP) '("62" "70"
		"71" "72"
		"73" "74"
		"76" "90"
	       )
       )
	(setq TXT (GET_TILE (CAADDR TMP)))
	(setq FILTER (APPEND
		       (CONS (CONS -4 (NTH (READ POP) '("=" "<"
					    ">" "<="
					    ">=" "<>"
					    "&" "&="
					   )
				      )
			     ) (LIST (CONS (READ (CAR TMP)) (READ TXT)))
		       )
		       FILTER
		     )
	)
      )
      ((MEMBER (CAR TMP) '("38" "39"
		"40" "41"
		"42" "43"
		"44" "48"
		"50" "51"
		"52"
	       )
       )
	(setq TXT (GET_TILE (CAADDR TMP)))
	(setq TXT1 (CADR (CADDR TMP)))
	(setq RC (READ (GET_TILE (CAR (LAST TMP)))))
	(if (/= TXT TXT1)
	  (PROGN
	    (setq TXT (ATOF TXT))
	  )
	  (PROGN
	    (setq TXT (DXF (READ (CAR TMP)) ENTL))
	  )
	)
	(if (AND
	      (OR
		(= (TYPE RC) 'REAL)
		(= (TYPE RC) 'INT)
	      )
	      (= POP "0")
	    )
	  (PROGN
	    (setq FILTER (APPEND
			   (CONS '(-4 . "<=") (LIST (CONS (READ
								(CAR TMP)
							  ) (+ TXT
							       (ABS RC)
							    )
						    )
					      )
			   )
			   (CONS '(-4 . ">=") (LIST (CONS (READ
								(CAR TMP)
							  ) (- TXT
							       (ABS RC)
							    )
						    )
					      )
			   )
			   FILTER
			 )
	    )
	  )
	  (PROGN
	    (setq FILTER (APPEND
			   (CONS (CONS -4 (NTH (READ POP) '("=" "<"
						">" "<="
						">=" "<>"
					       )
					  )
				 ) (LIST (CONS (READ (CAR TMP)) TXT))
			   )
			   FILTER
			 )
	    )
	  )
	)
      )
      ((MEMBER (CAR TMP) '("10" "11"))
	(setq TXT1 (GET_TILE (CAADDR TMP)))
	(setq TXT2 (GET_TILE (CAR (CADDDR TMP))))
	(setq TXT3 (GET_TILE (CAR (LAST TMP))))
	(setq POP_1 (NTH (READ POP) '("=" "<"
			  ">" "<="
			  ">=" "<>"
			 )
		    )
	)
	(setq POP_2 POP_1)
	(setq POP_3 POP_1)
	(if (= TXT1 "")
	  (PROGN
	    (setq POP_1 "*")
	  )
	)
	(if (= TXT2 "")
	  (PROGN
	    (setq POP_2 "*")
	  )
	)
	(if (= TXT3 "")
	  (PROGN
	    (setq POP_3 "*")
	  )
	)
	(if (/= TXT1 (CADR (CADDR TMP)))
	  (PROGN
	    (setq TXT1 (ATOF TXT1))
	  )
	  (PROGN
	    (setq TXT1 (CAR (DXF (READ (CAR TMP)) ENTL)))
	  )
	)
	(if (/= TXT2 (CADR (CADDDR TMP)))
	  (PROGN
	    (setq TXT2 (ATOF TXT2))
	  )
	  (PROGN
	    (setq TXT2 (CADR (DXF (READ (CAR TMP)) ENTL)))
	  )
	)
	(if (/= TXT3 (CADR (LAST TMP)))
	  (PROGN
	    (setq TXT3 (ATOF TXT3))
	  )
	  (PROGN
	    (setq TXT3 (CADDR (DXF (READ (CAR TMP)) ENTL)))
	  )
	)
	(setq FILTER (APPEND
		       (CONS (CONS -4 (STRCAT POP_1 "," POP_2 "," POP_3))
			     (LIST (CONS (READ (CAR TMP)) (LIST TXT1 TXT2
								TXT3
							  )
				   )
			     )
		       )
		       FILTER
		     )
	)
      )
    )
  )
  (if FJLST
    (PROGN
      (if (NULL FILTER)
	(PROGN
	  (setq FILTER (LIST (ASSOC 0 ENTL)))
	)
      )
      (setq FJFLT '(AND
		   )
      )
      (FOREACH TMP FJLST
	(setq POP (GET_TILE (CADR TMP)))
	(setq TXT (GET_TILE (CAADDR TMP)))
	(setq TXT1 (CADR (CADDR TMP)))
	(setq RC (READ (GET_TILE (CAR (LAST TMP)))))
	(if (/= TXT TXT1)
	  (PROGN
	    (if (/= "INSERT" (DXF 0 SLENT))
	      (PROGN
		(setq TXT (ATOF TXT))
	      )
	    )
	  )
	  (PROGN
	    (setq TXT (EVAL (CADR (DXF (CAR TMP) LST5))))
	  )
	)
	(if (AND
	      (OR
		(= (TYPE RC) 'REAL)
		(= (TYPE RC) 'INT)
	      )
	      (= POP "0")
	    )
	  (PROGN
	    (setq FJFLT (APPEND
			  FJFLT
			  (LIST (LIST 'AND (LIST '<= (CADR (DXF
								(CAR TMP)
								LST5
							   )
						     ) (+ TXT (ABS RC))
					   ) (LIST '>= (CADR (DXF
								  (CAR TMP)
								  LST5
							     )
						       ) (- TXT
							    (ABS RC)
							 )
					     )
				)
			  )
			)
	    )
	  )
	  (PROGN
	    (setq FJFLT (APPEND
			  FJFLT
			  (LIST (LIST (READ (NTH (READ POP) '("=" "<"
						  ">" "<="
						  ">=" "<>"
						 )
					    )
				      ) (CADR (DXF (CAR TMP) LST5)) TXT
				)
			  )
			)
	    )
	  )
	)
      )
    )
  )
)
(DEFUN SHOW_LIST (KEY LST)
  (START_LIST KEY)
  (MAPCAR
    'ADD_LIST
    LST
  )
  (END_LIST)
)
(DEFUN WRITE (F KTMP CODE VALUE TXT WIDTH / TMP)
  (setq TMP (STRCAT TXT (VL-PRINC-TO-STRING CODE)))
  (WRITE-LINE (STRCAT ":edit_box{value=\"" VALUE "\";key=\"" TMP
		      "\";edit_width=" WIDTH ";allow_accept=true;}"
	      ) F
  )
  (setq KTMP (CONS (LIST TMP VALUE) KTMP))
)
(DEFUN DXF (I ENT)
  (if (= (TYPE ENT) 'ENAME)
    (PROGN
      (setq ENT (ENTGET ENT))
    )
  )
  (CDR (ASSOC I ENT))
)
(DEFUN CHSGET (C01 / C02 C03 C04 C05)
  (if C01
    (PROGN
      (setq C02 0)
      (setq C03 (SSLENGTH C01))
      (while (and
	       (< C02 C03)
	     )
	(setq C04 (SSNAME C01 C02))
	(setq C02 (1+ C02))
	(setq C05 (CONS C04 C05))
      )
    )
  )
  C05
)
(DEFUN LEN (ENT)
  (if (= (TYPE ENT) 'ENAME)
    (PROGN
      (setq ENT (vlax-ename->vla-object ENT))
    )
  )
  (if (WCMATCH (vla-get-ObjectName ENT) "AcDbPolyline,AcDbEllipse,AcDbCircle,AcDbArc,AcDbLine,AcDb2dPolyline,AcDbSpline")
    (PROGN
      (vlax-curve-getDistAtParam ENT (vlax-curve-getEndParam ENT))
    )
  )
)
(DEFUN VXGETATTS (OBJ)
  (if (= (TYPE OBJ) 'ENAME)
    (PROGN
      (setq OBJ (vlax-ename->vla-object OBJ))
    )
  )
  (if (= (vla-get-ObjectName OBJ) "AcDbBlockReference")
    (PROGN
      (MAPCAR
	'(LAMBDA (ATT)
	   (CONS (vla-get-TagString ATT) (vla-get-TextString ATT))
	 )
	(vlax-invoke OBJ "GetAttributes")
      )
    )
  )
)
(DEFUN KLDC_1 (/ ATTL ALEN LVAL LTAG AFLST AA CC A11 A12 A13)
  (setq ATTL (VXGETATTS SLENT))
  (if ATTL
    (PROGN
      (setq ALEN (LENGTH ATTL))
      (while (and
	       (> ALEN 0)
	     )
	(setq A11 (LIST (CONS 'NTH (CONS (- ALEN 1) '((VXGETATTS SLENT))))))
	(setq A12 (CONS 'IF (LIST '(VXGETATTS SLENT) (CONS 'CDR A11))))
	(setq A13 (CONS 'IF (LIST '(VXGETATTS SLENT) (CONS 'CAR A11))))
	(setq LVAL (LIST (STRCAT "FJ" (RTOS (* 2 ALEN) 2 0)) "属性数值" A12))
	(setq LTAG (LIST (STRCAT "FJ" (RTOS (- (* 2 ALEN) 1) 2 0))
			 "属性标志" A13
		   )
	)
	(setq AFLST (APPEND
		      (LIST LVAL LTAG)
		      AFLST
		    )
	)
	(setq ALEN (1- ALEN))
      )
      (setq AA (ASSOC "INSERT" LST2))
      (setq CC (LIST (CAR AA) (CADR AA) (APPEND
					  '(FJ)
					  (REVERSE AFLST)
					)
	       )
      )
      (setq LST2 (SUBST
		   CC
		   AA
		   LST2
		 )
      )
    )
  )
)
(if (NOT (MEMBER "acopm.arx" (ARX)))
  (PROGN
    (ARXLOAD "acopm.arx")
  )
)
(PRINC)
(vl-ACAD-defun (DEFUN C:` (/ ENT)
		 (setq ENT (SSGET))
		 (if (/= ENT nil)
		   (PROGN
		     (command "change")
		     (command ENT)
		     (command "")
		     (command "p")
		     (command "c")
		     (command "bylayer")
		     (command "")
		   )
		   (PROGN
		     (SETVAR "CECOLOR" "bylayer")
		   )
		 )
	       )
)
'C:`
(vl-ACAD-defun (DEFUN C:1 (/ ENT)
		 (setq ENT (SSGET))
		 (if (/= ENT nil)
		   (PROGN
		     (command "change")
		     (command ENT)
		     (command "")
		     (command "p")
		     (command "c")
		     (command "1")
		     (command "")
		   )
		   (PROGN
		     (SETVAR "CECOLOR" "1")
		   )
		 )
	       )
)
'C:1
(vl-ACAD-defun (DEFUN C:2 (/ ENT)
		 (setq ENT (SSGET))
		 (if (/= ENT nil)
		   (PROGN
		     (command "change")
		     (command ENT)
		     (command "")
		     (command "p")
		     (command "c")
		     (command "2")
		     (command "")
		   )
		   (PROGN
		     (SETVAR "CECOLOR" "2")
		   )
		 )
	       )
)
'C:2
(vl-ACAD-defun (DEFUN C:3 (/ ENT)
		 (setq ENT (SSGET))
		 (if (/= ENT nil)
		   (PROGN
		     (command "change")
		     (command ENT)
		     (command "")
		     (command "p")
		     (command "c")
		     (command "3")
		     (command "")
		   )
		   (PROGN
		     (SETVAR "CECOLOR" "3")
		   )
		 )
	       )
)
'C:3
(vl-ACAD-defun (DEFUN C:4 (/ ENT)
		 (setq ENT (SSGET))
		 (if (/= ENT nil)
		   (PROGN
		     (command "change")
		     (command ENT)
		     (command "")
		     (command "p")
		     (command "c")
		     (command "4")
		     (command "")
		   )
		   (PROGN
		     (SETVAR "CECOLOR" "4")
		   )
		 )
	       )
)
'C:4
(vl-ACAD-defun (DEFUN C:5 (/ ENT)
		 (setq ENT (SSGET))
		 (if (/= ENT nil)
		   (PROGN
		     (command "change")
		     (command ENT)
		     (command "")
		     (command "p")
		     (command "c")
		     (command "5")
		     (command "")
		   )
		   (PROGN
		     (SETVAR "CECOLOR" "5")
		   )
		 )
	       )
)
'C:5
(vl-ACAD-defun (DEFUN C:6 (/ ENT)
		 (setq ENT (SSGET))
		 (if (/= ENT nil)
		   (PROGN
		     (command "change")
		     (command ENT)
		     (command "")
		     (command "p")
		     (command "c")
		     (command "6")
		     (command "")
		   )
		   (PROGN
		     (SETVAR "CECOLOR" "6")
		   )
		 )
	       )
)
'C:6
(vl-ACAD-defun (DEFUN C:7 (/ ENT)
		 (setq ENT (SSGET))
		 (if (/= ENT nil)
		   (PROGN
		     (command "change")
		     (command ENT)
		     (command "")
		     (command "p")
		     (command "c")
		     (command "7")
		     (command "")
		   )
		   (PROGN
		     (SETVAR "CECOLOR" "7")
		   )
		 )
	       )
)
'C:7
(vl-ACAD-defun (DEFUN C:8 (/ ENT)
		 (setq ENT (SSGET))
		 (if (/= ENT nil)
		   (PROGN
		     (command "change")
		     (command ENT)
		     (command "")
		     (command "p")
		     (command "c")
		     (command "8")
		     (command "")
		   )
		   (PROGN
		     (SETVAR "CECOLOR" "8")
		   )
		 )
	       )
)
'C:8
(vl-ACAD-defun (DEFUN C:9 (/ ENT)
		 (setq ENT (SSGET))
		 (if (/= ENT nil)
		   (PROGN
		     (command "change")
		     (command ENT)
		     (command "")
		     (command "p")
		     (command "c")
		     (command "9")
		     (command "")
		   )
		   (PROGN
		     (SETVAR "CECOLOR" "9")
		   )
		 )
	       )
)
'C:9
(vl-ACAD-defun (DEFUN C:10 (/ ENT)
		 (setq ENT (SSGET))
		 (if (/= ENT nil)
		   (PROGN
		     (command "change")
		     (command ENT)
		     (command "")
		     (command "p")
		     (command "c")
		     (command "10")
		     (command "")
		   )
		   (PROGN
		     (SETVAR "CECOLOR" "10")
		   )
		 )
	       )
)
'C:10
(vl-ACAD-defun (DEFUN C:11 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "11")
		 (command "")
	       )
)
'C:11
(vl-ACAD-defun (DEFUN C:12 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "12")
		 (command "")
	       )
)
'C:12
(vl-ACAD-defun (DEFUN C:13 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "13")
		 (command "")
	       )
)
'C:13
(vl-ACAD-defun (DEFUN C:14 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "14")
		 (command "")
	       )
)
'C:14
(vl-ACAD-defun (DEFUN C:15 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "15")
		 (command "")
	       )
)
'C:15
(vl-ACAD-defun (DEFUN C:16 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "16")
		 (command "")
	       )
)
'C:16
(vl-ACAD-defun (DEFUN C:17 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "17")
		 (command "")
	       )
)
'C:17
(vl-ACAD-defun (DEFUN C:18 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "18")
		 (command "")
	       )
)
'C:18
(vl-ACAD-defun (DEFUN C:19 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "19")
		 (command "")
	       )
)
'C:19
(vl-ACAD-defun (DEFUN C:20 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "20")
		 (command "")
	       )
)
'C:20
(vl-ACAD-defun (DEFUN C:21 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "21")
		 (command "")
	       )
)
'C:21
(vl-ACAD-defun (DEFUN C:22 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "22")
		 (command "")
	       )
)
'C:22
(vl-ACAD-defun (DEFUN C:23 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "23")
		 (command "")
	       )
)
'C:23
(vl-ACAD-defun (DEFUN C:24 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "24")
		 (command "")
	       )
)
'C:24
(vl-ACAD-defun (DEFUN C:25 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "25")
		 (command "")
	       )
)
'C:25
(vl-ACAD-defun (DEFUN C:26 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "26")
		 (command "")
	       )
)
'C:26
(vl-ACAD-defun (DEFUN C:27 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "27")
		 (command "")
	       )
)
'C:27
(vl-ACAD-defun (DEFUN C:28 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "28")
		 (command "")
	       )
)
'C:28
(vl-ACAD-defun (DEFUN C:29 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "29")
		 (command "")
	       )
)
'C:29
(vl-ACAD-defun (DEFUN C:30 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "30")
		 (command "")
	       )
)
'C:30
(vl-ACAD-defun (DEFUN C:31 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "31")
		 (command "")
	       )
)
'C:31
(vl-ACAD-defun (DEFUN C:32 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "32")
		 (command "")
	       )
)
'C:32
(vl-ACAD-defun (DEFUN C:33 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "33")
		 (command "")
	       )
)
'C:33
(vl-ACAD-defun (DEFUN C:34 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "34")
		 (command "")
	       )
)
'C:34
(vl-ACAD-defun (DEFUN C:35 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "35")
		 (command "")
	       )
)
'C:35
(vl-ACAD-defun (DEFUN C:36 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "36")
		 (command "")
	       )
)
'C:36
(vl-ACAD-defun (DEFUN C:37 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "37")
		 (command "")
	       )
)
'C:37
(vl-ACAD-defun (DEFUN C:38 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "38")
		 (command "")
	       )
)
'C:38
(vl-ACAD-defun (DEFUN C:39 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "39")
		 (command "")
	       )
)
'C:39
(vl-ACAD-defun (DEFUN C:40 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "40")
		 (command "")
	       )
)
'C:40
(vl-ACAD-defun (DEFUN C:41 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "41")
		 (command "")
	       )
)
'C:41
(vl-ACAD-defun (DEFUN C:42 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "42")
		 (command "")
	       )
)
'C:42
(vl-ACAD-defun (DEFUN C:43 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "43")
		 (command "")
	       )
)
'C:43
(vl-ACAD-defun (DEFUN C:44 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "44")
		 (command "")
	       )
)
'C:44
(vl-ACAD-defun (DEFUN C:45 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "45")
		 (command "")
	       )
)
'C:45
(vl-ACAD-defun (DEFUN C:46 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "46")
		 (command "")
	       )
)
'C:46
(vl-ACAD-defun (DEFUN C:47 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "47")
		 (command "")
	       )
)
'C:47
(vl-ACAD-defun (DEFUN C:48 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "48")
		 (command "")
	       )
)
'C:48
(vl-ACAD-defun (DEFUN C:49 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "49")
		 (command "")
	       )
)
'C:49
(vl-ACAD-defun (DEFUN C:50 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "50")
		 (command "")
	       )
)
'C:50
(vl-ACAD-defun (DEFUN C:51 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "51")
		 (command "")
	       )
)
'C:51
(vl-ACAD-defun (DEFUN C:52 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "52")
		 (command "")
	       )
)
'C:52
(vl-ACAD-defun (DEFUN C:53 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "53")
		 (command "")
	       )
)
'C:53
(vl-ACAD-defun (DEFUN C:54 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "54")
		 (command "")
	       )
)
'C:54
(vl-ACAD-defun (DEFUN C:55 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "55")
		 (command "")
	       )
)
'C:55
(vl-ACAD-defun (DEFUN C:56 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "56")
		 (command "")
	       )
)
'C:56
(vl-ACAD-defun (DEFUN C:57 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "57")
		 (command "")
	       )
)
'C:57
(vl-ACAD-defun (DEFUN C:58 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "58")
		 (command "")
	       )
)
'C:58
(vl-ACAD-defun (DEFUN C:59 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "59")
		 (command "")
	       )
)
'C:59
(vl-ACAD-defun (DEFUN C:60 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "60")
		 (command "")
	       )
)
'C:60
(vl-ACAD-defun (DEFUN C:61 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "61")
		 (command "")
	       )
)
'C:61
(vl-ACAD-defun (DEFUN C:62 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "62")
		 (command "")
	       )
)
'C:62
(vl-ACAD-defun (DEFUN C:63 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "63")
		 (command "")
	       )
)
'C:63
(vl-ACAD-defun (DEFUN C:64 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "64")
		 (command "")
	       )
)
'C:64
(vl-ACAD-defun (DEFUN C:65 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "65")
		 (command "")
	       )
)
'C:65
(vl-ACAD-defun (DEFUN C:66 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "66")
		 (command "")
	       )
)
'C:66
(vl-ACAD-defun (DEFUN C:67 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "67")
		 (command "")
	       )
)
'C:67
(vl-ACAD-defun (DEFUN C:68 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "68")
		 (command "")
	       )
)
'C:68
(vl-ACAD-defun (DEFUN C:69 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "69")
		 (command "")
	       )
)
'C:69
(vl-ACAD-defun (DEFUN C:70 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "70")
		 (command "")
	       )
)
'C:70
(vl-ACAD-defun (DEFUN C:71 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "71")
		 (command "")
	       )
)
'C:71
(vl-ACAD-defun (DEFUN C:72 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "72")
		 (command "")
	       )
)
'C:72
(vl-ACAD-defun (DEFUN C:73 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "73")
		 (command "")
	       )
)
'C:73
(vl-ACAD-defun (DEFUN C:74 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "74")
		 (command "")
	       )
)
'C:74
(vl-ACAD-defun (DEFUN C:75 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "75")
		 (command "")
	       )
)
'C:75
(vl-ACAD-defun (DEFUN C:76 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "76")
		 (command "")
	       )
)
'C:76
(vl-ACAD-defun (DEFUN C:77 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "77")
		 (command "")
	       )
)
'C:77
(vl-ACAD-defun (DEFUN C:78 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "78")
		 (command "")
	       )
)
'C:78
(vl-ACAD-defun (DEFUN C:79 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "79")
		 (command "")
	       )
)
'C:79
(vl-ACAD-defun (DEFUN C:80 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "80")
		 (command "")
	       )
)
'C:80
(vl-ACAD-defun (DEFUN C:81 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "81")
		 (command "")
	       )
)
'C:81
(vl-ACAD-defun (DEFUN C:82 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "82")
		 (command "")
	       )
)
'C:82
(vl-ACAD-defun (DEFUN C:83 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "83")
		 (command "")
	       )
)
'C:83
(vl-ACAD-defun (DEFUN C:84 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "84")
		 (command "")
	       )
)
'C:84
(vl-ACAD-defun (DEFUN C:85 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "85")
		 (command "")
	       )
)
'C:85
(vl-ACAD-defun (DEFUN C:86 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "86")
		 (command "")
	       )
)
'C:86
(vl-ACAD-defun (DEFUN C:87 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "87")
		 (command "")
	       )
)
'C:87
(vl-ACAD-defun (DEFUN C:88 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "88")
		 (command "")
	       )
)
'C:88
(vl-ACAD-defun (DEFUN C:89 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "89")
		 (command "")
	       )
)
'C:89
(vl-ACAD-defun (DEFUN C:90 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "90")
		 (command "")
	       )
)
'C:90
(vl-ACAD-defun (DEFUN C:91 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "91")
		 (command "")
	       )
)
'C:91
(vl-ACAD-defun (DEFUN C:92 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "92")
		 (command "")
	       )
)
'C:92
(vl-ACAD-defun (DEFUN C:93 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "93")
		 (command "")
	       )
)
'C:93
(vl-ACAD-defun (DEFUN C:94 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "94")
		 (command "")
	       )
)
'C:94
(vl-ACAD-defun (DEFUN C:95 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "95")
		 (command "")
	       )
)
'C:95
(vl-ACAD-defun (DEFUN C:96 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "96")
		 (command "")
	       )
)
'C:96
(vl-ACAD-defun (DEFUN C:97 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "97")
		 (command "")
	       )
)
'C:97
(vl-ACAD-defun (DEFUN C:98 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "98")
		 (command "")
	       )
)
'C:98
(vl-ACAD-defun (DEFUN C:99 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "99")
		 (command "")
	       )
)
'C:99
(vl-ACAD-defun (DEFUN C:100 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "100")
		 (command "")
	       )
)
'C:100
(vl-ACAD-defun (DEFUN C:101 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "101")
		 (command "")
	       )
)
'C:101
(vl-ACAD-defun (DEFUN C:102 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "102")
		 (command "")
	       )
)
'C:102
(vl-ACAD-defun (DEFUN C:103 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "103")
		 (command "")
	       )
)
'C:103
(vl-ACAD-defun (DEFUN C:104 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "104")
		 (command "")
	       )
)
'C:104
(vl-ACAD-defun (DEFUN C:105 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "105")
		 (command "")
	       )
)
'C:105
(vl-ACAD-defun (DEFUN C:106 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "106")
		 (command "")
	       )
)
'C:106
(vl-ACAD-defun (DEFUN C:107 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "107")
		 (command "")
	       )
)
'C:107
(vl-ACAD-defun (DEFUN C:108 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "108")
		 (command "")
	       )
)
'C:108
(vl-ACAD-defun (DEFUN C:109 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "109")
		 (command "")
	       )
)
'C:109
(vl-ACAD-defun (DEFUN C:110 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "110")
		 (command "")
	       )
)
'C:110
(vl-ACAD-defun (DEFUN C:111 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "111")
		 (command "")
	       )
)
'C:111
(vl-ACAD-defun (DEFUN C:112 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "112")
		 (command "")
	       )
)
'C:112
(vl-ACAD-defun (DEFUN C:113 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "113")
		 (command "")
	       )
)
'C:113
(vl-ACAD-defun (DEFUN C:114 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "114")
		 (command "")
	       )
)
'C:114
(vl-ACAD-defun (DEFUN C:115 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "115")
		 (command "")
	       )
)
'C:115
(vl-ACAD-defun (DEFUN C:116 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "116")
		 (command "")
	       )
)
'C:116
(vl-ACAD-defun (DEFUN C:117 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "117")
		 (command "")
	       )
)
'C:117
(vl-ACAD-defun (DEFUN C:118 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "118")
		 (command "")
	       )
)
'C:118
(vl-ACAD-defun (DEFUN C:119 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "119")
		 (command "")
	       )
)
'C:119
(vl-ACAD-defun (DEFUN C:120 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "120")
		 (command "")
	       )
)
'C:120
(vl-ACAD-defun (DEFUN C:121 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "121")
		 (command "")
	       )
)
'C:121
(vl-ACAD-defun (DEFUN C:122 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "122")
		 (command "")
	       )
)
'C:122
(vl-ACAD-defun (DEFUN C:123 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "123")
		 (command "")
	       )
)
'C:123
(vl-ACAD-defun (DEFUN C:124 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "124")
		 (command "")
	       )
)
'C:124
(vl-ACAD-defun (DEFUN C:125 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "125")
		 (command "")
	       )
)
'C:125
(vl-ACAD-defun (DEFUN C:126 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "126")
		 (command "")
	       )
)
'C:126
(vl-ACAD-defun (DEFUN C:127 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "127")
		 (command "")
	       )
)
'C:127
(vl-ACAD-defun (DEFUN C:128 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "128")
		 (command "")
	       )
)
'C:128
(vl-ACAD-defun (DEFUN C:129 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "129")
		 (command "")
	       )
)
'C:129
(vl-ACAD-defun (DEFUN C:130 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "130")
		 (command "")
	       )
)
'C:130
(vl-ACAD-defun (DEFUN C:131 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "131")
		 (command "")
	       )
)
'C:131
(vl-ACAD-defun (DEFUN C:132 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "132")
		 (command "")
	       )
)
'C:132
(vl-ACAD-defun (DEFUN C:133 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "133")
		 (command "")
	       )
)
'C:133
(vl-ACAD-defun (DEFUN C:134 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "134")
		 (command "")
	       )
)
'C:134
(vl-ACAD-defun (DEFUN C:135 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "135")
		 (command "")
	       )
)
'C:135
(vl-ACAD-defun (DEFUN C:136 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "136")
		 (command "")
	       )
)
'C:136
(vl-ACAD-defun (DEFUN C:137 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "137")
		 (command "")
	       )
)
'C:137
(vl-ACAD-defun (DEFUN C:138 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "138")
		 (command "")
	       )
)
'C:138
(vl-ACAD-defun (DEFUN C:139 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "139")
		 (command "")
	       )
)
'C:139
(vl-ACAD-defun (DEFUN C:140 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "140")
		 (command "")
	       )
)
'C:140
(vl-ACAD-defun (DEFUN C:141 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "141")
		 (command "")
	       )
)
'C:141
(vl-ACAD-defun (DEFUN C:142 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "142")
		 (command "")
	       )
)
'C:142
(vl-ACAD-defun (DEFUN C:143 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "143")
		 (command "")
	       )
)
'C:143
(vl-ACAD-defun (DEFUN C:144 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "144")
		 (command "")
	       )
)
'C:144
(vl-ACAD-defun (DEFUN C:145 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "145")
		 (command "")
	       )
)
'C:145
(vl-ACAD-defun (DEFUN C:146 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "146")
		 (command "")
	       )
)
'C:146
(vl-ACAD-defun (DEFUN C:147 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "147")
		 (command "")
	       )
)
'C:147
(vl-ACAD-defun (DEFUN C:148 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "148")
		 (command "")
	       )
)
'C:148
(vl-ACAD-defun (DEFUN C:149 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "149")
		 (command "")
	       )
)
'C:149
(vl-ACAD-defun (DEFUN C:150 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "150")
		 (command "")
	       )
)
'C:150
(vl-ACAD-defun (DEFUN C:151 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "151")
		 (command "")
	       )
)
'C:151
(vl-ACAD-defun (DEFUN C:152 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "152")
		 (command "")
	       )
)
'C:152
(vl-ACAD-defun (DEFUN C:153 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "153")
		 (command "")
	       )
)
'C:153
(vl-ACAD-defun (DEFUN C:154 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "154")
		 (command "")
	       )
)
'C:154
(vl-ACAD-defun (DEFUN C:155 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "155")
		 (command "")
	       )
)
'C:155
(vl-ACAD-defun (DEFUN C:156 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "156")
		 (command "")
	       )
)
'C:156
(vl-ACAD-defun (DEFUN C:157 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "167")
		 (command "")
	       )
)
'C:157
(vl-ACAD-defun (DEFUN C:158 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "158")
		 (command "")
	       )
)
'C:158
(vl-ACAD-defun (DEFUN C:159 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "159")
		 (command "")
	       )
)
'C:159
(vl-ACAD-defun (DEFUN C:160 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "160")
		 (command "")
	       )
)
'C:160
(vl-ACAD-defun (DEFUN C:161 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "161")
		 (command "")
	       )
)
'C:161
(vl-ACAD-defun (DEFUN C:162 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "162")
		 (command "")
	       )
)
'C:162
(vl-ACAD-defun (DEFUN C:163 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "163")
		 (command "")
	       )
)
'C:163
(vl-ACAD-defun (DEFUN C:164 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "164")
		 (command "")
	       )
)
'C:164
(vl-ACAD-defun (DEFUN C:165 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "165")
		 (command "")
	       )
)
'C:165
(vl-ACAD-defun (DEFUN C:166 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "166")
		 (command "")
	       )
)
'C:166
(vl-ACAD-defun (DEFUN C:167 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "167")
		 (command "")
	       )
)
'C:167
(vl-ACAD-defun (DEFUN C:168 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "168")
		 (command "")
	       )
)
'C:168
(vl-ACAD-defun (DEFUN C:169 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "169")
		 (command "")
	       )
)
'C:169
(vl-ACAD-defun (DEFUN C:170 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "170")
		 (command "")
	       )
)
'C:170
(vl-ACAD-defun (DEFUN C:171 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "171")
		 (command "")
	       )
)
'C:171
(vl-ACAD-defun (DEFUN C:172 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "172")
		 (command "")
	       )
)
'C:172
(vl-ACAD-defun (DEFUN C:173 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "173")
		 (command "")
	       )
)
'C:173
(vl-ACAD-defun (DEFUN C:174 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "174")
		 (command "")
	       )
)
'C:174
(vl-ACAD-defun (DEFUN C:175 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "175")
		 (command "")
	       )
)
'C:175
(vl-ACAD-defun (DEFUN C:176 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "176")
		 (command "")
	       )
)
'C:176
(vl-ACAD-defun (DEFUN C:177 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "177")
		 (command "")
	       )
)
'C:177
(vl-ACAD-defun (DEFUN C:178 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "178")
		 (command "")
	       )
)
'C:178
(vl-ACAD-defun (DEFUN C:179 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "179")
		 (command "")
	       )
)
'C:179
(vl-ACAD-defun (DEFUN C:180 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "180")
		 (command "")
	       )
)
'C:180
(vl-ACAD-defun (DEFUN C:181 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "181")
		 (command "")
	       )
)
'C:181
(vl-ACAD-defun (DEFUN C:182 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "182")
		 (command "")
	       )
)
'C:182
(vl-ACAD-defun (DEFUN C:183 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "183")
		 (command "")
	       )
)
'C:183
(vl-ACAD-defun (DEFUN C:184 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "184")
		 (command "")
	       )
)
'C:184
(vl-ACAD-defun (DEFUN C:185 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "185")
		 (command "")
	       )
)
'C:185
(vl-ACAD-defun (DEFUN C:186 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "186")
		 (command "")
	       )
)
'C:186
(vl-ACAD-defun (DEFUN C:187 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "187")
		 (command "")
	       )
)
'C:187
(vl-ACAD-defun (DEFUN C:188 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "188")
		 (command "")
	       )
)
'C:188
(vl-ACAD-defun (DEFUN C:189 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "189")
		 (command "")
	       )
)
'C:189
(vl-ACAD-defun (DEFUN C:190 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "190")
		 (command "")
	       )
)
'C:190
(vl-ACAD-defun (DEFUN C:191 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "191")
		 (command "")
	       )
)
'C:191
(vl-ACAD-defun (DEFUN C:192 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "192")
		 (command "")
	       )
)
'C:192
(vl-ACAD-defun (DEFUN C:193 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "193")
		 (command "")
	       )
)
'C:193
(vl-ACAD-defun (DEFUN C:194 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "194")
		 (command "")
	       )
)
'C:194
(vl-ACAD-defun (DEFUN C:195 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "195")
		 (command "")
	       )
)
'C:195
(vl-ACAD-defun (DEFUN C:196 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "196")
		 (command "")
	       )
)
'C:196
(vl-ACAD-defun (DEFUN C:197 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "197")
		 (command "")
	       )
)
'C:197
(vl-ACAD-defun (DEFUN C:198 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "198")
		 (command "")
	       )
)
'C:198
(vl-ACAD-defun (DEFUN C:199 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "199")
		 (command "")
	       )
)
'C:199
(vl-ACAD-defun (DEFUN C:200 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "byblock")
		 (command "")
	       )
)
'C:200
(vl-ACAD-defun (DEFUN C:201 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "201")
		 (command "")
	       )
)
'C:201
(vl-ACAD-defun (DEFUN C:202 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "202")
		 (command "")
	       )
)
'C:202
(vl-ACAD-defun (DEFUN C:203 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "203")
		 (command "")
	       )
)
'C:203
(vl-ACAD-defun (DEFUN C:204 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "204")
		 (command "")
	       )
)
'C:204
(vl-ACAD-defun (DEFUN C:205 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "205")
		 (command "")
	       )
)
'C:205
(vl-ACAD-defun (DEFUN C:206 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "206")
		 (command "")
	       )
)
'C:206
(vl-ACAD-defun (DEFUN C:207 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "207")
		 (command "")
	       )
)
'C:207
(vl-ACAD-defun (DEFUN C:208 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "208")
		 (command "")
	       )
)
'C:208
(vl-ACAD-defun (DEFUN C:209 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "209")
		 (command "")
	       )
)
'C:209
(vl-ACAD-defun (DEFUN C:210 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "210")
		 (command "")
	       )
)
'C:210
(vl-ACAD-defun (DEFUN C:211 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "211")
		 (command "")
	       )
)
'C:211
(vl-ACAD-defun (DEFUN C:212 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "212")
		 (command "")
	       )
)
'C:212
(vl-ACAD-defun (DEFUN C:213 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "213")
		 (command "")
	       )
)
'C:213
(vl-ACAD-defun (DEFUN C:214 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "214")
		 (command "")
	       )
)
'C:214
(vl-ACAD-defun (DEFUN C:215 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "215")
		 (command "")
	       )
)
'C:215
(vl-ACAD-defun (DEFUN C:216 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "216")
		 (command "")
	       )
)
'C:216
(vl-ACAD-defun (DEFUN C:217 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "217")
		 (command "")
	       )
)
'C:217
(vl-ACAD-defun (DEFUN C:218 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "218")
		 (command "")
	       )
)
'C:218
(vl-ACAD-defun (DEFUN C:219 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "219")
		 (command "")
	       )
)
'C:219
(vl-ACAD-defun (DEFUN C:220 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "220")
		 (command "")
	       )
)
'C:220
(vl-ACAD-defun (DEFUN C:221 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "221")
		 (command "")
	       )
)
'C:221
(vl-ACAD-defun (DEFUN C:222 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "222")
		 (command "")
	       )
)
'C:222
(vl-ACAD-defun (DEFUN C:223 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "223")
		 (command "")
	       )
)
'C:223
(vl-ACAD-defun (DEFUN C:224 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "224")
		 (command "")
	       )
)
'C:224
(vl-ACAD-defun (DEFUN C:225 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "225")
		 (command "")
	       )
)
'C:225
(vl-ACAD-defun (DEFUN C:226 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "226")
		 (command "")
	       )
)
'C:226
(vl-ACAD-defun (DEFUN C:227 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "227")
		 (command "")
	       )
)
'C:227
(vl-ACAD-defun (DEFUN C:228 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "228")
		 (command "")
	       )
)
'C:228
(vl-ACAD-defun (DEFUN C:229 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "229")
		 (command "")
	       )
)
'C:229
(vl-ACAD-defun (DEFUN C:230 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "230")
		 (command "")
	       )
)
'C:230
(vl-ACAD-defun (DEFUN C:231 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "231")
		 (command "")
	       )
)
'C:231
(vl-ACAD-defun (DEFUN C:232 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "232")
		 (command "")
	       )
)
'C:232
(vl-ACAD-defun (DEFUN C:233 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "233")
		 (command "")
	       )
)
'C:233
(vl-ACAD-defun (DEFUN C:234 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "234")
		 (command "")
	       )
)
'C:234
(vl-ACAD-defun (DEFUN C:235 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "235")
		 (command "")
	       )
)
'C:235
(vl-ACAD-defun (DEFUN C:236 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "236")
		 (command "")
	       )
)
'C:236
(vl-ACAD-defun (DEFUN C:237 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "237")
		 (command "")
	       )
)
'C:237
(vl-ACAD-defun (DEFUN C:238 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "238")
		 (command "")
	       )
)
'C:238
(vl-ACAD-defun (DEFUN C:239 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "239")
		 (command "")
	       )
)
'C:239
(vl-ACAD-defun (DEFUN C:240 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "240")
		 (command "")
	       )
)
'C:240
(vl-ACAD-defun (DEFUN C:241 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "241")
		 (command "")
	       )
)
'C:241
(vl-ACAD-defun (DEFUN C:242 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "242")
		 (command "")
	       )
)
'C:242
(vl-ACAD-defun (DEFUN C:243 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "243")
		 (command "")
	       )
)
'C:243
(vl-ACAD-defun (DEFUN C:244 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "244")
		 (command "")
	       )
)
'C:244
(vl-ACAD-defun (DEFUN C:245 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "245")
		 (command "")
	       )
)
'C:245
(vl-ACAD-defun (DEFUN C:246 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "246")
		 (command "")
	       )
)
'C:246
(vl-ACAD-defun (DEFUN C:247 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "247")
		 (command "")
	       )
)
'C:247
(vl-ACAD-defun (DEFUN C:248 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "248")
		 (command "")
	       )
)
'C:248
(vl-ACAD-defun (DEFUN C:249 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "249")
		 (command "")
	       )
)
'C:249
(vl-ACAD-defun (DEFUN C:250 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "250")
		 (command "")
	       )
)
'C:250
(vl-ACAD-defun (DEFUN C:251 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "251")
		 (command "")
	       )
)
'C:251
(vl-ACAD-defun (DEFUN C:252 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "252")
		 (command "")
	       )
)
'C:252
(vl-ACAD-defun (DEFUN C:253 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "253")
		 (command "")
	       )
)
'C:253
(vl-ACAD-defun (DEFUN C:254 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "254")
		 (command "")
	       )
)
'C:254
(vl-ACAD-defun (DEFUN C:255 (/ ENT)
		 (setq ENT (SSGET))
		 (command "change")
		 (command ENT)
		 (command "")
		 (command "p")
		 (command "c")
		 (command "255")
		 (command "")
	       )
)
'C:255
(defun c:MOYU_YM_YY (/ EP1 EG1 EG2 blktag EP1st blkname str GETK prefix sttr
             index0 index sslist XZ_sortlist len0 len sslist-ptl index0)
  (vl-load-com)
  (if (progn
        (setq EP1 (entsel"\n点取属性块中页码的位置:")
              EG1 (cdr (assoc 0 (entget (car EP1)))))
        (if (= EG1 "INSERT")
          (progn 
            (setq EG2 (car (nentselp (cadr EP1))))
            (if (= (cdr (assoc 0 (entget EG2))) "ATTRIB")
              (setq blktag (cdr (assoc 2 (entget EG2)))))
            (setq EP1st (entget (car EP1))
                  blkname (assoc 2 EP1st)))))
      (princ (strcat "\n块名为-->" (cdr blkname) "   标记为-->" blktag))
      (exit))

  (setq prefix (getstring "\n请输入前缀:"))
  (if (= str0 nil) (setq str0 1))
  (initget 6)
  (setq str (getint (strcat "\n请输入一个起始整数<" (rtos str0) ">:")))
  (if (= str nil)(setq str str0))
  
  (initget "H V S")
  (setq GETK (getkword "\n排序方式 [横向优先(H)/竖向优先(V)/选择优先(S)]: <H> "))
  
  (setq ss (ssget (cons blkname slist))
        index0 0 
        index (sslength ss) 
        sslist '())
  
  (repeat index
    (setq sslist (cons (ssname ss index0) sslist)
          index0 (1+ index0)))
  
  (setq index0 0 sslist-ptl '() tmp-pt '())
  (repeat index
    (setq tmp-pt (cons (nth index0 sslist) 
                       (cons (cdr(assoc 10 (entget (nth index0 sslist)))) tmp-pt))
          sslist-ptl (cons tmp-pt sslist-ptl)
          tmp-pt '()
          index0 (1+ index0)))
  
  (cond      
    ((or (= GETK "H")(= GETK nil))
     (setq XZ_sortlist (vl-sort
                        (vl-sort sslist-ptl '(lambda (s1 s2) (> (cadadr s1) (cadadr s2))))
                        '(lambda (s3 s4) (if(equal (cadadr s3) (cadadr s4) 0.6)(< (caadr s3) (caadr s4)))))))
    ((= GETK "V")
     (setq XZ_sortlist (vl-sort
                        (vl-sort sslist-ptl '(lambda (s1 s2) (< (caadr s1) (caadr s2))))
                        '(lambda (s3 s4) (if(equal (caadr s3) (caadr s4) 0.6)(> (cadadr s3) (cadadr s4)))))))
    ((= GETK "S")
     (setq XZ_sortlist sslist-ptl)))
  
  (setq len0 0 len (length XZ_sortlist))
  (repeat len
    (if (setq ent0 (car (nth len0 XZ_sortlist)))
      (progn
        (cond
          ((/= prefix "")
           (if (=(strlen (rtos str))1)
             (setq sttr (strcat prefix "0" (rtos str)))
             (setq sttr (strcat prefix (rtos str)))))
          ((= prefix "")(setq sttr (rtos str))))
        (xz-att ent0 blktag sttr)
        (setq len0 (1+ len0) str (1+ str) str0 str))))
  (prin1))

(defun xz-att (ent tag string / liST0 liST1 num blkref)
  (vl-load-com)
  (if (= (vla-Get-ObjectName (setq blkref (vlax-Ename->vla-Object ent))) "AcDbBlockReference")
    (if (vla-Get-HasAttributes blkref)
      (progn 
        (setq liST0 (vlax-safearray->list (vlax-variant-value (vla-GetAttributes blkref)))
              liST1 (mapcar 'vla-Get-TagString liST0)
              num (vl-position tag list1))
        (vla-put-TextString (nth num liST0) string))
    ))
  (prin1))
'C:MOYU_YM_YY
(DEFUN MAKE-LWPOLYLINE (LST / PT)
  (ENTMAKE (APPEND
	     (LIST '(0 . "LWPOLYLINE") '(100 . "AcDbEntity") '(100 . "AcDbPolyline")
		   (CONS 90 (LENGTH LST))
	     )
	     (MAPCAR
	       '(LAMBDA (PT)
		  (CONS 10 PT)
		)
	       LST
	     )
	   )
  )
)
(DEFUN PNTS:MINY->MAXX (PTS TOL / PT1 PT2)
  (VL-SORT PTS '(LAMBDA (PT1 PT2)
		  (IF (EQUAL (CADR PT1) (CADR PT2) TOL)
		    (IF (EQUAL (CAR PT1) (CAR PT2) TOL)
		      (< (CADDR PT1) (CADDR PT2))
		      (> (CAR PT1) (CAR PT2))
		    )
		    (< (CADR PT1) (CADR PT2))
		  )
		)
  )
)
(DEFUN DPTLST (ENN / ENT LST)
  (setq ENT (ENTGET ENN))
  (setq LST (LIST))
  (FOREACH X ENT
    (if (= (CAR X) 10)
      (PROGN
	(setq LST (CONS (CDR X) LST))
      )
    )
  )
  LST
)
'C:MOYU_RRNA_YY
(DEFUN FFR (EN R)
  (SETVAR "FILLETRAD" R)
  (command "_.FILLET")
  (command "P")
  (command EN)
)
(DEFUN RER (SN / X SN_G P42)
  (setq SN_G (ENTGET SN))
  (setq SN_G (MAPCAR
	       '(LAMBDA (X)
		  (IF (= (CAR X) 42)
		    (SETQ X (CONS 42 (- 0 (CDR X))))
		    X
		  )
		)
	       SN_G
	     )
  )
  (ENTMOD SN_G)
)

(DEFUN GXL-GETSAMPLET (CURVE D / PERDISTTOLINE GETPOINTS NAME DXF PL I)
  (DEFUN PERDISTTOLINE (PT P1 P2 / NORM)
    (setq NORM (MAPCAR
		 '-
		 P2
		 P1
	       )
    )
    (setq P1 (TRANS P1 0 NORM))
    (setq PT (TRANS PT 0 NORM))
    (ABS (- (CAR PT) (CAR P1)))
  )
  (DEFUN GETPOINTS (CURVE STPAR ENPAR D / PS PE PM)
    (setq PS (vlax-curve-getPointAtParam CURVE STPAR))
    (setq PE (vlax-curve-getPointAtParam CURVE ENPAR))
    (setq PM (vlax-curve-getPointAtParam CURVE (* 0.5 (+ STPAR ENPAR))))
    (if PM
      (PROGN
	(if (<= (PERDISTTOLINE PM PS PE) D)
	  (PROGN
	    (LIST PE)
	  )
	  (PROGN
	    (APPEND
	      (GETPOINTS CURVE STPAR (* 0.5 (+ STPAR ENPAR)) D)
	      (GETPOINTS CURVE (* 0.5 (+ STPAR ENPAR)) ENPAR D)
	    )
	  )
	)
      )
      (PROGN
	(LIST PE)
      )
    )
  )
  (if (= 'VLA-OBJECT (TYPE CURVE))
    (PROGN
      (setq CURVE (vlax-vla-object->ename CURVE))
    )
  )
  (COND
    ((= "LINE" (setq NAME (CDR (ASSOC 0 (setq DXF (ENTGET CURVE))))))
      (LIST (vlax-curve-getStartPoint CURVE)
	    (vlax-curve-getEndPoint CURVE)
      )
    )
    ((= "ARC" NAME)
      (CONS (vlax-curve-getStartPoint CURVE) (GETPOINTS CURVE
							(vlax-curve-getStartParam CURVE)
							(vlax-curve-getEndParam CURVE)
							D
					     )
      )
    )
    ((= "CIRCLE" NAME)
      (CONS (vlax-curve-getStartPoint CURVE) (APPEND
					       (GETPOINTS CURVE 0 PI D)
					       (GETPOINTS CURVE PI
							  (* 2 PI) D
					       )
					     )
      )
    )
    ((= "ELLIPSE" NAME)
      (if (vlax-curve-isClosed CURVE)
	(PROGN
	  (CONS (vlax-curve-getStartPoint CURVE) (APPEND
						   (GETPOINTS CURVE 0 PI D)
						   (GETPOINTS CURVE PI
							      (* 2 PI) D
						   )
						 )
	  )
	)
	(PROGN
	  (CONS (vlax-curve-getStartPoint CURVE) (GETPOINTS CURVE
							    (vlax-curve-getStartParam CURVE)
							    (vlax-curve-getEndParam CURVE)
							    D
						 )
	  )
	)
      )
    )
    ((= "SPLINE" NAME)
      (setq PL (MAPCAR
		 'CDR
		 (VL-REMOVE-IF-NOT '(LAMBDA (X)
				      (= 11 (CAR X))
				    ) DXF
		 )
	       )
      )
      (if (NOT PL)
	(PROGN
	  (setq PL (MAPCAR
		     '(LAMBDA (X)
			(vlax-curve-getClosestPointTo CURVE (CDR X))
		      )
		     (VL-REMOVE-IF-NOT '(LAMBDA (X)
					  (= 10 (CAR X))
					) DXF
		     )
		   )
	  )
	)
      )
      (setq PL (MAPCAR
		 '(LAMBDA (X)
		    (vlax-curve-getParamAtPoint CURVE X)
		  )
		 PL
	       )
      )
      (if (EQUAL (CAR PL) (LAST PL) 1.0e-06)
	(PROGN
	  (setq PL (REVERSE (CONS (vlax-curve-getEndParam CURVE)
				  (CDR (REVERSE PL))
			    )
		   )
	  )
	)
      )
      (setq PL (MAPCAR
		 'LIST
		 PL
		 (CDR PL)
	       )
      )
      (setq PL (APPLY
		 'APPEND
		 (MAPCAR
		   '(LAMBDA (X)
		      (LIST (LIST (CAR X) (* 0.5 (APPLY
						   (QUOTE +)
						   X
						 )
					  )
			    ) (LIST (* 0.5 (APPLY
					     (QUOTE +)
					     X
					   )
				    ) (CADR X)
			      )
		      )
		    )
		   PL
		 )
	       )
      )
      (CONS (vlax-curve-getStartPoint CURVE) (APPLY
					       'APPEND
					       (MAPCAR
						 '(LAMBDA (X)
						    (APPLY
						      (QUOTE GETPOINTS)
						      (APPEND
							(CONS CURVE X)
							(LIST D)
						      )
						    )
						  )
						 PL
					       )
					     )
      )
    )
    ((WCMATCH NAME "*POLYLINE")
      (setq PL nil)
      (setq I -1)
      (while (and
	       (< I (vlax-curve-getEndParam CURVE))
	     )
	(setq PL (CONS (setq I (1+ I))
		       PL
		 )
	)
      )
      (setq PL (REVERSE PL))
      (setq PL (MAPCAR
		 'LIST
		 PL
		 (CDR PL)
	       )
      )
      (CONS (vlax-curve-getStartPoint CURVE) (APPLY
					       'APPEND
					       (MAPCAR
						 '(LAMBDA (X)
						    (APPLY
						      (QUOTE GETPOINTS)
						      (APPEND
							(CONS CURVE X)
							(LIST D)
						      )
						    )
						  )
						 PL
					       )
					     )
      )
    )
  )
)
(DEFUN LM:OUTLINE (SEL / A APP ARE BOX CMD DIS EL EN ENL ENLBOX ENLST ENT
		       LST N O OBJ OLSS PL RTN SS SSBOX SUMLST TMP X Y ENO
		  )
  (if (setq BOX (LM:SSBOUNDINGBOX SEL))
    (PROGN
      (setq APP (vlax-get-acad-object))
      (setq DIS (/ (APPLY
		     'DISTANCE
		     BOX
		   ) 20.0
		)
      )
      (setq LST (MAPCAR
		  '(LAMBDA (A O)
		     (MAPCAR
		       O
		       A
		       (LIST DIS DIS)
		     )
		   )
		  BOX
		  '(- +)
		)
      )
      (setq ARE (APPLY
		  '*
		  (APPLY
		    'MAPCAR
		    (CONS '- (REVERSE LST))
		  )
		)
      )
      (setq DIS (* DIS 1.5))
      (setq ENT (ENTMAKEX (APPEND
			    '((0 . "LWPOLYLINE") (100 . "AcDbEntity")
			     (100 . "AcDbPolyline")
			     (90 . 4)
			     (70 . 1)
			    )
			    (MAPCAR
			      '(LAMBDA (X)
				 (CONS 10 (MAPCAR
					    (QUOTE (LAMBDA (Y)
						     ((EVAL Y) LST)
						   )
					    )
					    X
					  )
				 )
			       )
			      '((CAAR CADAR) (CAADR CADAR)
			       (CAADR CADADR)
			       (CAAR CADADR)
			      )
			    )
			  )
		)
      )
      (APPLY
	'vlax-invoke
	(VL-LIST* APP 'ZOOMWINDOW (MAPCAR
				    '(LAMBDA (A O)
				       (MAPCAR
					 O
					 A
					 (LIST DIS DIS 0.0)
				       )
				     )
				    BOX
				    '(- +)
				  )
	)
      )
      (setq SSBOX (SSGET "c" (CAR BOX) (CADR BOX)))
      (setq CMD (GETVAR 'CMDECHO))
      (setq ENL (ENTLAST))
      (setq RTN (SSADD))
      (while (and
	       (setq TMP (ENTNEXT ENL))
	     )
	(setq ENL TMP)
      )
      (SETVAR 'CMDECHO 0)
      (command "_.-boundary")
      (command "_a")
      (command "_b")
      (command "_n")
      (command SEL)
      (command ENT)
      (command "")
      (command "_i")
      (command "_y")
      (command "_o")
      (command "_p")
      (command "")
      (command "_non")
      (command (TRANS (MAPCAR
			'-
			(CAR BOX)
			(LIST (/ DIS 3.0) (/ DIS 3.0))
		      ) 0 1
	       )
      )
      (command "")
      (while (and
	       (< 0 (GETVAR 'CMDACTIVE))
	     )
	(command "")
      )
      (ENTDEL ENT)
      (while (and
	       (setq ENL (ENTNEXT ENL))
	     )
	(if (AND
	      (vlax-property-available-p (setq OBJ
					       (vlax-ename->vla-object ENL)
					 )
					 'AREA
	      )
	      (EQUAL (vla-get-Area OBJ) ARE 0.0001)
	    )
	  (PROGN
	    (ENTDEL ENL)
	  )
	  (PROGN
	    (SSADD ENL RTN)
	  )
	)
      )
      (setq ENLBOX (YJ-SS2LST SSBOX))
      (FOREACH X ENLBOX
	(REDRAW X 2)
      )
      (setq OLSS RTN)
      (setq EL (YJ-SS2LST OLSS))
      (setq EL (MAPCAR
		 '(LAMBDA (X)
		    (IF (= (YJ-DXF 0 X) "REGION")
		      (YJ-REGION2PLINE X)
		      X
		    )
		  )
		 EL
	       )
      )
      (setq SUMLST nil)
      (setq N 0)
      (REPEAT (LENGTH EL)
	(setq EN (NTH N EL))
	(setq ENO (CAR (YJ-OFFSET EN "W" 0.01 "n")))
	(setq PL (GXL-GETSAMPLET ENO 0.5))
	(ENTDEL ENO)
	(if (setq SS (SSGET "wp" PL))
	  (PROGN
	    (setq ENLST (YJ-SS2LST SS))
	    (setq ENLST (VL-REMOVE EN ENLST))
	    (setq SUMLST (APPEND
			   ENLST
			   SUMLST
			 )
	    )
	  )
	)
	(setq N (1+ N))
      )
      (if (> (LENGTH SUMLST) 0)
	(PROGN
	  (setq SUMLST (YJ_UN-REPEAT-LIST SUMLST))
	  (FOREACH N SUMLST
	    (vla-Delete (vlax-ename->vla-object N))
	  )
	  (setq EL (YJ-LST-SUBTRACT EL SUMLST))
	)
      )
      (FOREACH X ENLBOX
	(REDRAW X 1)
      )
      (vla-ZoomPrevious APP)
      (SETVAR 'CMDECHO CMD)
      EL
    )
  )
)
(DEFUN LM:SSBOUNDINGBOX (S / A B I M N O)
  (REPEAT (setq I (SSLENGTH S))
    (if (AND
	  (setq O (vlax-ename->vla-object (SSNAME S (setq I (1- I)))))
	  (vlax-method-applicable-p O 'GETBOUNDINGBOX)
	  (NOT (VL-CATCH-ALL-ERROR-P (VL-CATCH-ALL-APPLY 'vla-GetBoundingBox
							 (LIST O 'A 'B)
				     )
	       )
	  )
	)
      (PROGN
	(setq M (CONS (vlax-safearray->list A) M))
	(setq N (CONS (vlax-safearray->list B) N))
      )
    )
  )
  (if (AND
	M
	N
      )
    (PROGN
      (MAPCAR
	'(LAMBDA (A B)
	   (APPLY
	     (QUOTE MAPCAR)
	     (CONS A B)
	   )
	 )
	'(MIN
	   MAX
	 )
	(LIST M N)
      )
    )
  )
)
(DEFUN LM:STARTUNDO (DOC)
  (LM:ENDUNDO DOC)
  (vla-StartUndoMark DOC)
)
(DEFUN LM:ENDUNDO (DOC)
  (while (and
	   (= 8 (LOGAND 8 (GETVAR 'UNDOCTL)))
	 )
    (vla-EndUndoMark DOC)
  )
)
(DEFUN LM:ACDOC ()
  (EVAL (LIST 'DEFUN 'LM:ACDOC nil (vla-get-ActiveDocument
							   (vlax-get-acad-object)
				   )
	)
  )
  (LM:ACDOC)
)
(VL-LOAD-COM)
(PRINC)
(DEFUN GXL-GETSAMPLET (CURVE D / PERDISTTOLINE GETPOINTS NAME DXF PL I)
  (DEFUN PERDISTTOLINE (PT P1 P2 / NORM)
    (setq NORM (MAPCAR
		 '-
		 P2
		 P1
	       )
    )
    (setq P1 (TRANS P1 0 NORM))
    (setq PT (TRANS PT 0 NORM))
    (ABS (- (CAR PT) (CAR P1)))
  )
  (DEFUN GETPOINTS (CURVE STPAR ENPAR D / PS PE PM)
    (setq PS (vlax-curve-getPointAtParam CURVE STPAR))
    (setq PE (vlax-curve-getPointAtParam CURVE ENPAR))
    (setq PM (vlax-curve-getPointAtParam CURVE (* 0.5 (+ STPAR ENPAR))))
    (if PM
      (PROGN
	(if (<= (PERDISTTOLINE PM PS PE) D)
	  (PROGN
	    (LIST PE)
	  )
	  (PROGN
	    (APPEND
	      (GETPOINTS CURVE STPAR (* 0.5 (+ STPAR ENPAR)) D)
	      (GETPOINTS CURVE (* 0.5 (+ STPAR ENPAR)) ENPAR D)
	    )
	  )
	)
      )
      (PROGN
	(LIST PE)
      )
    )
  )
  (if (= 'VLA-OBJECT (TYPE CURVE))
    (PROGN
      (setq CURVE (vlax-vla-object->ename CURVE))
    )
  )
  (COND
    ((= "LINE" (setq NAME (CDR (ASSOC 0 (setq DXF (ENTGET CURVE))))))
      (LIST (vlax-curve-getStartPoint CURVE)
	    (vlax-curve-getEndPoint CURVE)
      )
    )
    ((= "ARC" NAME)
      (CONS (vlax-curve-getStartPoint CURVE) (GETPOINTS CURVE
							(vlax-curve-getStartParam CURVE)
							(vlax-curve-getEndParam CURVE)
							D
					     )
      )
    )
    ((= "CIRCLE" NAME)
      (CONS (vlax-curve-getStartPoint CURVE) (APPEND
					       (GETPOINTS CURVE 0 PI D)
					       (GETPOINTS CURVE PI
							  (* 2 PI) D
					       )
					     )
      )
    )
    ((= "ELLIPSE" NAME)
      (if (vlax-curve-isClosed CURVE)
	(PROGN
	  (CONS (vlax-curve-getStartPoint CURVE) (APPEND
						   (GETPOINTS CURVE 0 PI D)
						   (GETPOINTS CURVE PI
							      (* 2 PI) D
						   )
						 )
	  )
	)
	(PROGN
	  (CONS (vlax-curve-getStartPoint CURVE) (GETPOINTS CURVE
							    (vlax-curve-getStartParam CURVE)
							    (vlax-curve-getEndParam CURVE)
							    D
						 )
	  )
	)
      )
    )
    ((= "SPLINE" NAME)
      (setq PL (MAPCAR
		 'CDR
		 (VL-REMOVE-IF-NOT '(LAMBDA (X)
				      (= 11 (CAR X))
				    ) DXF
		 )
	       )
      )
      (if (NOT PL)
	(PROGN
	  (setq PL (MAPCAR
		     '(LAMBDA (X)
			(vlax-curve-getClosestPointTo CURVE (CDR X))
		      )
		     (VL-REMOVE-IF-NOT '(LAMBDA (X)
					  (= 10 (CAR X))
					) DXF
		     )
		   )
	  )
	)
      )
      (setq PL (MAPCAR
		 '(LAMBDA (X)
		    (vlax-curve-getParamAtPoint CURVE X)
		  )
		 PL
	       )
      )
      (if (EQUAL (CAR PL) (LAST PL) 1.0e-06)
	(PROGN
	  (setq PL (REVERSE (CONS (vlax-curve-getEndParam CURVE)
				  (CDR (REVERSE PL))
			    )
		   )
	  )
	)
      )
      (setq PL (MAPCAR
		 'LIST
		 PL
		 (CDR PL)
	       )
      )
      (setq PL (APPLY
		 'APPEND
		 (MAPCAR
		   '(LAMBDA (X)
		      (LIST (LIST (CAR X) (* 0.5 (APPLY
						   (QUOTE +)
						   X
						 )
					  )
			    ) (LIST (* 0.5 (APPLY
					     (QUOTE +)
					     X
					   )
				    ) (CADR X)
			      )
		      )
		    )
		   PL
		 )
	       )
      )
      (CONS (vlax-curve-getStartPoint CURVE) (APPLY
					       'APPEND
					       (MAPCAR
						 '(LAMBDA (X)
						    (APPLY
						      (QUOTE GETPOINTS)
						      (APPEND
							(CONS CURVE X)
							(LIST D)
						      )
						    )
						  )
						 PL
					       )
					     )
      )
    )
    ((WCMATCH NAME "*POLYLINE")
      (setq PL nil)
      (setq I -1)
      (while (and
	       (< I (vlax-curve-getEndParam CURVE))
	     )
	(setq PL (CONS (setq I (1+ I))
		       PL
		 )
	)
      )
      (setq PL (REVERSE PL))
      (setq PL (MAPCAR
		 'LIST
		 PL
		 (CDR PL)
	       )
      )
      (CONS (vlax-curve-getStartPoint CURVE) (APPLY
					       'APPEND
					       (MAPCAR
						 '(LAMBDA (X)
						    (APPLY
						      (QUOTE GETPOINTS)
						      (APPEND
							(CONS CURVE X)
							(LIST D)
						      )
						    )
						  )
						 PL
					       )
					     )
      )
    )
  )
)
(DEFUN YJ-REGION2PLINE (EN / E0 LN LNN NAME ODLST PL PT1 PT2 SEL X)
  (DEFUN LST2SS (LST / I SS)
    (setq SS (SSADD))
    (while (and
	     LST
	   )
      (setq SS (SSADD (CAR LST) SS))
      (setq LST (CDR LST))
    )
    SS
  )
  (DEFUN EXPLODEX (ENTS)
    (VL-CMDF "qaflags" 1 ".explode" ENTS "" "qaflags" 0)
    (while (and
	     (setq ENTS (SSGET "P" '((0 . "*POLYLINE"))))
	   )
      (EXPLODEX ENTS)
    )
  )
  (DEFUN NEW_LIST (E / LST)
    (while (and
	     (setq E (ENTNEXT E))
	   )
      (if (NOT (MEMBER (CDR (ASSOC 0 (ENTGET E))) '("ATTRIB" "VERTEX"
			"SEQEND"
		       )
	       )
	  )
	(PROGN
	  (setq LST (CONS E LST))
	)
      )
    )
    LST
  )
  (setq ODLST (MAPCAR
		'GETVAR
		'("cmdecho" "osmode"
		 "peditaccept"
		)
	      )
  )
  (MAPCAR
    'SETVAR
    '("cmdecho" "osmode"
     "peditaccept"
    )
    '(0 0 1)
  )
  (setq E0 (ENTLAST))
  (EXPLODEX EN)
  (setq LN (NEW_LIST E0))
  (setq LN (MAPCAR
	     '(LAMBDA (X)
		(SETQ NAME (CDR (ASSOC 0 (ENTGET X))))
		(SETQ PL (GXL-GETSAMPLET X 0.5))
		(IF (/= NAME "ARC")
		  (PROGN
		    (SETQ LNN (MAPCAR
				(QUOTE (LAMBDA (PT1 PT2)
					 (ENTMAKE (LIST (QUOTE (0 . "LINE"))
							(CONS 10 PT1)
							(CONS 11 PT2)
						  )
					 )
					 (ENTLAST)
				       )
				)
				PL
				(CDR PL)
			      )
		    )
		    (ENTDEL X)
		  )
		  (SETQ LNN (LIST X))
		)
		LNN
	      )
	     LN
	   )
  )
  (setq LN (APPLY
	     'APPEND
	     LN
	   )
  )
  (setq SEL (LST2SS LN))
  (command "_.pedit")
  (command "_m")
  (command SEL)
  (command "")
  (command "_j")
  (command "")
  (command "")
  (MAPCAR
    'SETVAR
    '("cmdecho" "osmode"
     "peditaccept"
    )
    ODLST
  )
  (ENTLAST)
)
(DEFUN YJ_UN-REPEAT-LIST (LST / A LST2)
  (setq A (CAR LST))
  (setq LST2 (CONS A LST2))
  (while (and
	   (setq LST (VL-REMOVE A LST))
	 )
  )
  (REVERSE LST2)
)
(DEFUN YJ-SS2LST (SS / I L)
  (if SS
    (PROGN
      (REPEAT (setq I (SSLENGTH SS))
	(setq L (CONS (SSNAME SS (setq I (1- I))) L))
      )
    )
  )
)
(DEFUN YJ-DXF (KEY ENAME)
  (CDR (ASSOC KEY (ENTGET ENAME)))
)
(DEFUN YJ-LST-SUBTRACT (LST1 LST2 / LST)
  (setq LST (APPEND
	      LST1
	      LST2
	    )
  )
  (VL-REMOVE-IF '(LAMBDA (X)
		   (MEMBER X (CDR (MEMBER X LST)))
		 ) LST
  )
)
(DEFUN YJ-OFFSET (SS KD0 OFFSET KD / N EN TMP NEW ENLST LW OLDLAY)
  (DEFUN CLOCKWISEP (EN / LW MINP MAXP LST)
    (setq LW (vlax-ename->vla-object EN))
    (vla-GetBoundingBox LW 'MINP 'MAXP)
    (setq MINP (vlax-safearray->list MINP))
    (setq MAXP (vlax-safearray->list MAXP))
    (setq LST (MAPCAR
		'(lambda (X)
		   (vlax-curve-getParamAtPoint LW
					       (vlax-curve-getClosestPointTo LW X)
		   )
		 )
		(LIST MINP (LIST (CAR MINP) (CADR MAXP)) MAXP (LIST
								    (CAR MAXP)
								    (CADR MINP)
							      )
		)
	      )
    )
    (if (OR
	  (<= (CAR LST) (CADR LST) (CADDR LST) (CADDDR LST))
	  (<= (CADR LST) (CADDR LST) (CADDDR LST) (CAR LST))
	  (<= (CADDR LST) (CADDDR LST) (CAR LST) (CADR LST))
	  (<= (CADDDR LST) (CAR LST) (CADR LST) (CADDR LST))
	)
      (PROGN
	T
      )
    )
  )
  (setq OLDLAY (GETVAR "CLAYER"))
  (if (= (TYPE SS) 'ENAME)
    (PROGN
      (setq TMP (SSADD))
      (SSADD SS TMP)
      (setq SS TMP)
    )
  )
  (setq ENLST nil)
  (REPEAT (setq N (SSLENGTH SS))
    (setq EN (SSNAME SS (setq N (1- N))))
    (COND
      ((OR
	 (= "ARC" (CDR (ASSOC 0 (ENTGET EN))))
	 (= "CIRCLE" (CDR (ASSOC 0 (ENTGET EN))))
       )
	(COND
	  ((= KD0 "W")
	    (vla-Offset (vlax-ename->vla-object EN) OFFSET)
	    (vla-put-Layer (vlax-ename->vla-object (setq NEW
							 (ENTLAST)
						   )
			   ) OLDLAY
	    )
	    (setq ENLST (CONS NEW ENLST))
	  )
	  ((= KD0 "N")
	    (vla-Offset (vlax-ename->vla-object EN) (- OFFSET))
	    (vla-put-Layer (vlax-ename->vla-object (setq NEW
							 (ENTLAST)
						   )
			   ) OLDLAY
	    )
	    (setq ENLST (CONS NEW ENLST))
	  )
	  (T
	    (vla-Offset (vlax-ename->vla-object EN) OFFSET)
	    (vla-put-Layer (vlax-ename->vla-object (setq NEW
							 (ENTLAST)
						   )
			   ) OLDLAY
	    )
	    (setq ENLST (CONS NEW ENLST))
	    (vla-Offset (vlax-ename->vla-object EN) (- OFFSET))
	    (vla-put-Layer (vlax-ename->vla-object (setq NEW
							 (ENTLAST)
						   )
			   ) OLDLAY
	    )
	    (setq ENLST (CONS NEW ENLST))
	  )
	)
      )
      (T
	(COND
	  ((= KD0 "W")
	    (if (CLOCKWISEP EN)
	      (PROGN
		(vla-Offset (vlax-ename->vla-object EN) (- OFFSET))
	      )
	      (PROGN
		(vla-Offset (vlax-ename->vla-object EN) OFFSET)
	      )
	    )
	    (vla-put-Layer (vlax-ename->vla-object (setq NEW
							 (ENTLAST)
						   )
			   ) OLDLAY
	    )
	    (setq ENLST (CONS NEW ENLST))
	  )
	  ((= KD0 "N")
	    (if (CLOCKWISEP EN)
	      (PROGN
		(vla-Offset (vlax-ename->vla-object EN) OFFSET)
	      )
	      (PROGN
		(vla-Offset (vlax-ename->vla-object EN) (- OFFSET))
	      )
	    )
	    (vla-put-Layer (vlax-ename->vla-object (setq NEW
							 (ENTLAST)
						   )
			   ) OLDLAY
	    )
	    (setq ENLST (CONS NEW ENLST))
	  )
	  (T
	    (vla-Offset (vlax-ename->vla-object EN) OFFSET)
	    (vla-put-Layer (vlax-ename->vla-object (setq NEW
							 (ENTLAST)
						   )
			   ) OLDLAY
	    )
	    (setq ENLST (CONS NEW ENLST))
	    (vla-Offset (vlax-ename->vla-object EN) (- OFFSET))
	    (vla-put-Layer (vlax-ename->vla-object (setq NEW
							 (ENTLAST)
						   )
			   ) OLDLAY
	    )
	    (setq ENLST (CONS NEW ENLST))
	  )
	)
      )
    )
    (if (= KD "Y")
      (PROGN
	(ENTDEL EN)
      )
    )
  )
  ENLST
)
(DEFUN YY_KAISHI_YY ()
  (setq *OLDERR* *ERROR*)
  (setq *ERROR* *MYERR*)
  (setq OSM_OLD (GETVAR "OSMODE"))
  (setq CLA_OLD (GETVAR "CLAYER"))
  (setq ORT_OLD (GETVAR "ORTHOMODE"))
)
(DEFUN YY_END_YY ()
  (SETVAR "OSMODE" OSM_OLD)
  (SETVAR "CLAYER" CLA_OLD)
  (SETVAR "ORTHOMODE" ORT_OLD)
  (SETVAR "CMDECHO" 1)
)
(DEFUN *MYERR* (MSG)
  (SETVAR "OSMODE" OSM_OLD)
  (SETVAR "CLAYER" CLA_OLD)
  (SETVAR "ORTHOMODE" ORT_OLD)
  (setq *ERROR* *OLDERR*)
  (if (= MSG "完美退出。谢谢使用。")
    (PROGN
      (PRINC (STRCAT "\\n>>>" MSG))
    )
    (PROGN
      (PRINC "\\n>>>中途退出，图层及捕捉已恢复。")
    )
  )
  (PRINC)
)
(setq *LST-1TH* (LIST     (LIST "基础工具"
        (LIST "绘制直线" "sbq0001" "MOYU_FF_YY" "FF")
        (LIST "绘制矩形" "sbq0002" "MOYU_R_YY" "R")
        (LIST "绘制椭圆" "sbq0003" "MOYU_FTY_YY" "FTY")
        (LIST "绘多边形" "sbq0004" "MOYU_FDB_YY" "FDB")
        (LIST "绘多段线" "sbq0005" "MOYU_FDD_YY" "FDD")
        (LIST "移动工具" "sbq0006" "MOYU_W_YY" "W")
        (LIST "偏移工具" "sbq0007" "MOYU_Q_YY" "Q")
        (LIST "旋转工具" "sbq0008" "MOYU_RR_YY" "RR")
        (LIST "镜像工具" "sbq0009" "MOYU_WR_YY" "WR")
        (LIST "倒角工具" "sbq0010" "MOYU_CF_YY" "CF")
        (LIST "定数等分" "sbq0011" "MOYU_DV_YY" "DV")
    )
    (LIST "高级工具"
        (LIST "复制旋转" "sbq0015" "MOYU_CCX_YY" "CCX")
        (LIST "连续复制" "sbq0016" "MOYU_CCC_YY" "CCC")
		(LIST "文字删重" "sbq0117" "MOYU_TC_YY" "TC")
		(LIST "一键统计" "sbq0217" "MOYU_TH_YY" "TH")
		(LIST "框选统计" "sbq0317" "MOYU_KH_YY" "KH")
        (LIST "区域删除" "sbq0018" "MOYU_TRR_YY" "TRR")
        (LIST "批量倒角" "sbq0019" "MOYU_FPL_YY" "FPL")
        (LIST "区域内偏" "sbq0020" "MOYU_QXN_YY" "QXN")
    )
    (LIST "标注工具"
        (LIST "线性标注" "sbq0021" "MOYU_DA_YY" "DA")
        (LIST "连续快注" "sbq0022" "MOYU_DC_YY" "DC")
        (LIST "快速标注" "sbq0023" "MOYU_DD_YY" "DD")
        (LIST "连续标注" "sbq0024" "MOYU_DLX_YY" "DLX")
        (LIST "删除标注" "sbq0026" "MOYU_DSB_YY" "DSB")
        (LIST "多重引线" "sbq0028" "MOYU_DZ_YY" "DZ")
        (LIST "对齐标注" "sbq0029" "MOYU_DXB_YY" "DXB")
        (LIST "角度标注" "sbq0030" "MOYU_DJD_YY" "DJD")
        (LIST "半径标注" "sbq0031" "MOYU_DBJ_YY" "DBJ")
        (LIST "直径标注" "sbq0032" "MOYU_DZJ_YY" "DZJ")
        (LIST "弧长标注" "sbq0033" "MOYU_DHC_YY" "DHC")
        (LIST "基线标注" "sbq0034" "MOYU_DJX_YY" "DJX")
    )
    (LIST "辅助工具"
        (LIST "绘构造线" "sbq0035" "MOYU_XX_YY" "XX")
        (LIST "横构造线" "sbq0036" "MOYU_XC_YY" "XC")
        (LIST "竖构造线" "sbq0037" "MOYU_XZ_YY" "XZ")
        (LIST "Z轴归零" "sbq0038" "MOYU_Z0_YY" "Z0")
        (LIST "线改虚线" "sbq0039" "MOYU_FFX_YY" "FFX")
        (LIST "批量页码" "sbq0040" "MOYU_YM_YY" "YM")
        (LIST "置为最上" "sbq0041" "MOYU_ZZS_YY" "DZS")
        (LIST "置为最底" "sbq0042" "MOYU_ZZX_YY" "DZX")
        (LIST "格式刷" "sbq0043" "MOYU_ZZ_YY" "ZZ")
    )
    (LIST "图块文字"
        (LIST "快速建块" "sbq0044" "MOYU_BB_YY" "BB")
        (LIST "炸开图块" "sbq0045" "MOYU_BX_YY" "BX")
        (LIST "图块统计" "sbq0046" "MOYU_BTJ_YY" "BTJ")
        (LIST "改块基点" "sbq0047" "MOYU_BGD_YY" "BGD")
        (LIST "按块全选" "sbq0048" "MOYU_BQS_YY" "BQS")
        (LIST "图块改名" "sbq0049" "MOYU_BGM_YY" "BGM")
        (LIST "文字替换" "sbq0050" "MOYU_TTH_YY" "TTH")
        (LIST "单行转多" "sbq0051" "MOYU_TZD_YY" "TZD")
        (LIST "超级改字" "sbq0052" "MOYU_TT_YY" "TT")
        (LIST "批改字宽" "sbq0053" "MOYU_TZK_YY" "TZK")
		)
		)
)
(DEFUN EDIT_BOX_3TH (LST-3TH)
  (LIST "            :edit_box {"
	"                horizontal_margin = none ;"
	"                vertical_margin = none ;"
	"                edit_limit = 5 ;"
	"                edit_width = 3.5 ;"
	"                fixed_width = true ;" (STRCAT "                key =\""
						       (CADR LST-3TH) "\" ;"
					       ) (STRCAT "                label =\""
							 (CAR LST-3TH)
							 "\" ;"
						 )
	"                width = 16 ;" "            }"
  )
)
(DEFUN EDIT_BOX_2TH (LST-2TH)
  (APPEND
    (LIST "        :boxed_column {" (STRCAT "            label =\""
					    (CAR LST-2TH) "\" ;"
				    )
    )
    (APPLY
      'APPEND
      (MAPCAR
	'EDIT_BOX_3TH
	(CDR LST-2TH)
      )
    )
    (LIST "              }")
  )
)
(DEFUN EDIT_BOX_1TH (*LST-1TH*)
  (APPEND
    (LIST "  agtckz1 : dialog{" "    label =\"自定义快捷键命令\" ;"
	  "    :row {"
    )
    (APPLY
      'APPEND
      (MAPCAR
	'EDIT_BOX_2TH
	*LST-1TH*
      )
    )
    (LIST "    }" "    :row {" "        :button {"
	  "            horizontal_margin = none ;"
	  "            vertical_margin = none ;"
	  "            fixed_width = true ;"
	  "            key =\"onekeyset\" ;"
	  "            label =\"快速设置\" ;" "            width = 14.2 ;"
	  "        }" "        :button {"
	  "            horizontal_margin = none ;"
	  "            vertical_margin = none ;"
	  "            fixed_width = true ;"
	  "            key =\"purgeall\" ;"
	  "            label =\"全部清除\" ;" "            width = 14.2 ;"
	  "        }" "        :spacer {" "            width = 50 ;"
	  "        }" "        :button {"
	  "            horizontal_margin = none ;"
	  "            vertical_margin = none ;"
	  "            fixed_width = true ;" "            key =\"ok\" ;"
	  "            label =\"确定\" ;" "            width = 10 ;"
	  "        }" "        :button {"
	  "            horizontal_margin = none ;"
	  "            vertical_margin = none ;"
	  "            fixed_width = true ;" "            key =\"find\" ;"
	  "            label =\"主页\" ;" "            width = 10 ;"
	  "        }" "        :button {"
	  "            horizontal_margin = none ;"
	  "            vertical_margin = none ;"
	  "            fixed_width = true ;"
	  "            is_cancel = true ;" "            key =\"cancle\" ;"
	  "            label =\"关闭\" ;" "            width = 10 ;"
	  "        }" "    }" "}"
    )
  )
)
(VL-LOAD-COM)
(vl-ACAD-defun (DEFUN C:MOYU_DIY_YY ()
		 (HOTKEYSET *LST-1TH*)
	       )
)
'C:MOYU_DIY_YY
(DEFUN HOTKEYSET (*LST-1TH* / DD FNAME FN X DCLID LIN)
  (DEFUN ONEKEYSET-MOYU ()
    (ONEKEYSET *LST-1TH*)
  )
  (DEFUN PURGEALL-MOYU ()
    (PURGEALL *LST-1TH*)
  )
  (DEFUN READ_FROM_SBQ-MOYU ()
    (READ_FROM_SBQ *LST-1TH*)
  )
  (setq FNAME (VL-FILENAME-MKTEMP nil nil ".dcl"))
  (setq FN (OPEN FNAME "w"))
  (FOREACH X (EDIT_BOX_1TH *LST-1TH*)
    (PRINC X FN)
    (WRITE-LINE "\n" FN)
  )
  (CLOSE FN)
  (setq FN (OPEN FNAME "r"))
  (setq DCLID (LOAD_DIALOG FNAME))
  (NEW_DIALOG "agtckz1" DCLID)
  (READ_FROM_REGISTRY *LST-1TH*)
  (WRITE_TO_SBQ *LST-1TH*)
  (MODE_TILE "cancle" 2)
  (ACTION_TILE "onekeyset" "(onekeyset-MOYU)")
  (ACTION_TILE "purgeall" "(purgeall-MOYU)")
  (ACTION_TILE "ok" "(read_from_sbq-MOYU) (done_dialog 1001)")
  (ACTION_TILE "find" "(done_dialog 1002)")
  (setq DD (START_DIALOG))
  (COND
    ((= DD 1001)
      (WRITE_TO_REGISTRY *LST-1TH*)
      (ALERT "★提示：\n自定义快捷命令已设置成功！\n重新打开AutoCAD后，设置才会全部生效！")
      (SBQ_QIDONG *LST-1TH*)
    )
    ((= DD 1002)
      (if C:YYMOYU
	(PROGN
	  (C:YYMOYU)
	)
      )
    )
  )
  (UNLOAD_DIALOG DCLID)
  (CLOSE FN)
  (VL-FILE-DELETE FNAME)
  (PRINC)
)
(DEFUN ONEKEYSET (*LST-1TH*)
  (PURGEALL *LST-1TH*)
  (MAPCAR
    '(LAMBDA (X)
       (SET_TILE (CADR X) (IF (CADDDR X)
			    (CADDDR X)
			    ""
			  )
       )
     )
    (APPLY
      'APPEND
      (MAPCAR
	'CDR
	*LST-1TH*
      )
    )
  )
)
(DEFUN PURGEALL (*LST-1TH*)
  (MAPCAR
    '(LAMBDA (X)
       (SET_TILE (CADR X) "")
     )
    (APPLY
      'APPEND
      (MAPCAR
	'CDR
	*LST-1TH*
      )
    )
  )
)
(DEFUN READ_FROM_REGISTRY (LST-1TH*)
  (MAPCAR
    '(LAMBDA (X)
       (IF (AND
	     X
	     (CADR X)
	     (CADDR X)
	   )
	 (EVAL (READ (STRCAT "(setq rfreg_" (CADR X) " (vl-registry-read \"HKEY_CURRENT_USER\\\\MOYU_Hotkey\" \""
			     (CADR X) "\"))"
		     )
	       )
	 )
       )
     )
    (APPLY
      'APPEND
      (MAPCAR
	'CDR
	LST-1TH*
      )
    )
  )
)
(DEFUN WRITE_TO_SBQ (*LST-1TH*)
  (MAPCAR
    '(LAMBDA (X)
       (EVAL (READ (STRCAT "(if rfreg_" (CADR X) " (set_tile \""
			   (CADR X) "\" rfreg_" (CADR X) ") (set_tile \""
			   (CADR X) "\" \"\"))"
		   )
	     )
       )
     )
    (APPLY
      'APPEND
      (MAPCAR
	'CDR
	*LST-1TH*
      )
    )
  )
)
(DEFUN READ_FROM_SBQ (*LST-1TH*)
  (MAPCAR
    '(LAMBDA (X)
       (EVAL (READ (STRCAT "(setq rfw_" (CADR X) " (get_tile \""
			   (CADR X) "\"))"
		   )
	     )
       )
     )
    (APPLY
      'APPEND
      (MAPCAR
	'CDR
	*LST-1TH*
      )
    )
  )
)
(DEFUN WRITE_TO_REGISTRY (LST-1TH*)
  (MAPCAR
    '(LAMBDA (X)
       (IF (AND
	     X
	     (CADR X)
	     (CADDR X)
	   )
	 (PROGN
	   (EVAL (READ (STRCAT "(vl-registry-write" " " "\""
			       "HKEY_CURRENT_USER\\\\MOYU_Hotkey" "\"" " "
			       "\"" (CADR X) "\"" " " "rfw_" (CADR X) ")"
		       )
		 )
	   )
	   (IF (AND
		 (= (EVAL (READ (STRCAT "rfw_" (CADR X)))) "")
	       )
	     (PROGN
	       (VL-REGISTRY-DELETE "HKEY_CURRENT_USER\\MOYU_Hotkey"
				   (CADR X)
	       )
	     )
	   )
	 )
       )
     )
    (APPLY
      'APPEND
      (MAPCAR
	'CDR
	LST-1TH*
      )
    )
  )
)
(DEFUN SBQ_QIDONG (*LST-1TH*)
  (SETVAR "cmdecho" 0)
  (VL-LOAD-COM)
  (READ_FROM_REGISTRY *LST-1TH*)
  (MAPCAR
    '(LAMBDA (X)
       (EVAL (READ (STRCAT "(if rfreg_" (CADR X)
			   " (eval (read (strcat \"(defun c:\" rfreg_"
			   (CADR X) " \"() (c:" (CADDR X) "))\"))))"
		   )
	     )
       )
     )
    (APPLY
      'APPEND
      (MAPCAR
	'CDR
	*LST-1TH*
      )
    )
  )
  (PRINC)
  (if (GETVAR "HPISLANDDETECTIONMODE")
    (PROGN
      (SETVAR "HPISLANDDETECTIONMODE" 1)
    )
  )
  (if (GETVAR "HPISLANDDETECTION")
    (PROGN
      (SETVAR "HPISLANDDETECTION" 0)
    )
  )
  (PRINC)
)
(SBQ_QIDONG *LST-1TH*)
(DEFUN WRITE_DCL_DQ (/ DCL_FILE FILE STR)
  (setq DCL_FILE (VL-FILENAME-MKTEMP nil nil ".Dcl"))
  (setq FILE (OPEN DCL_FILE "w"))
  (FOREACH STR '("dq:dialog{" " label=\"实体对齐\";"
     " :row{" "  :button{label=\" 左对齐 \";key=\"hl\";width=13;height=2.5;allow_accept=true;}"
     "  :button{label=\"水平居中\";key=\"hm\";width=13;height=2.5;allow_accept=true;}" "  :button{label=\" 右对齐 \";key=\"hr\";width=13;height=2.5;allow_accept=true;}"
     " }" " :row{"
     "  :button{label=\" 上对齐 \";key=\"vt\";width=13;height=2.5;allow_accept=true;}" "  :button{label=\"垂直居中\";key=\"vm\";width=13;height=2.5;allow_accept=true;}"
     "  :button{label=\" 下对齐 \";key=\"vd\";width=13;height=2.5;allow_accept=true;}"
     " }"
     "  :button{label=\" 关闭 \";key=\"cancel\";width=13;height=2.2;is_cancel=true;fixed_width=true;alignment=centered;}"
     "}"
    )
    (WRITE-LINE STR FILE)
  )
  (CLOSE FILE)
  DCL_FILE
)
(PRINC)
(DEFUN PROCESS (AMODE / APNT APNT_X APNT_Y COUNT OBJNAME VLAXOBJ MINPOINT
		      MAXPOINT MINEXT MAXEXT EXT_L EXT_R EXT_M EXT_VT EXT_VD
		      EXT_VM TPNT
	       )
  (setq OLDCMDECHO (GETVAR "cmdecho"))
  (SETVAR "cmdecho" 0)
  (setq SELOBJS (SSGET))
  (if (OR
	(NOT SELOBJS)
	(= (SSLENGTH SELOBJS) 1)
      )
    (PROGN
      (PRINC "\n你必须选定两个或两个以上的对象")
    )
  )
  (INITGET 1)
  (setq APNT (GETPOINT "\n选择对齐点："))
  (setq APNT_X (CAR APNT))
  (setq APNT_Y (CADR APNT))
  (VL-LOAD-COM)
  (setq COUNT 0)
  (REPEAT (SSLENGTH SELOBJS)
    (setq OBJNAME (SSNAME SELOBJS COUNT))
    (setq VLAXOBJ (vlax-ename->vla-object OBJNAME))
    (setq MINPOINT (vlax-make-variant))
    (setq MAXPOINT (vlax-make-variant))
    (vla-GetBoundingBox VLAXOBJ 'MINPOINT 'MAXPOINT)
    (setq MINEXT (vlax-safearray->list MINPOINT))
    (setq MAXEXT (vlax-safearray->list MAXPOINT))
    (setq EXT_L (CAR MINEXT))
    (setq EXT_R (CAR MAXEXT))
    (setq EXT_M (+ (/ (ABS (- EXT_L EXT_R)) 2) EXT_L))
    (setq EXT_VD (CADR MINEXT))
    (setq EXT_VT (CADR MAXEXT))
    (setq EXT_VM (+ (/ (ABS (- EXT_VT EXT_VD)) 2) EXT_VD))
    (COND
      ((= AMODE "HL")
	(setq TPNT (LIST EXT_L APNT_Y))
      )
      ((= AMODE "HM")
	(setq TPNT (LIST EXT_M APNT_Y))
      )
      ((= AMODE "HR")
	(setq TPNT (LIST EXT_R APNT_Y))
      )
      ((= AMODE "VT")
	(setq TPNT (LIST APNT_X EXT_VT))
      )
      ((= AMODE "VM")
	(setq TPNT (LIST APNT_X EXT_VM))
      )
      ((= AMODE "VD")
	(setq TPNT (LIST APNT_X EXT_VD))
      )
    )
    (if TPNT
      (PROGN
	(command "_move")
	(command OBJNAME)
	(command "")
	(command "non")
	(command TPNT)
	(command "non")
	(command APNT)
      )
    )
    (setq COUNT (1+ COUNT))
  )
  (SETVAR "cmdecho" OLDCMDECHO)
)

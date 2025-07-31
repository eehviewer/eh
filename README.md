 return () => clearInterval(ti mer);
    }, [ti meLeft, isSubmitted]);
 
    // 自动提交（时间到）
    const autoSubmit = () => {
        handleSubmit();
    };
 
    // 处理答案变化
    const handleAnswerChange = (questionId, value) => {
        setAnswers(prev => ({
               prev,
            [questionId]: value
        }));
    };
 
    // 提交考试
    const handleSubmit = async () => {
        if (isSubmitted) return;
        
        try {
            // 验证是否所有必答题都已回答
            const unanswered = questions filter(q => {
                const answer = answers[q question_id];
                if (q type === 'single_choice' || q type === 'true_false') {
                    return !answer;
                } else if (q type === 'multiple_choice') {
                    return !answer || answer split(',') length === 0;
                }
                return answer === null || answer trim() === '';
            });
            
            if (unanswered length > 0) {
                const confirmSubmit = window confirm(
                    `您还有${unanswered length}道题未作答，确定要提交吗？`
                );
                if (!confirmSubmit) return;
            }
            
            // 准备提交数据
            const submitData = {
                exam_id: examId,
                user_id: userId,
                answers: questions map(q => ({
                    question_id: q question_id,
                    answer: answers[q question_id]
                })),
                submit_ti me: new Date() toISOString()
            };
            
            await axios post('/api/exam-records', submitData);
            setIsSubmitted(true);
            
        } catch (err) {
            setError('提交失败，请重试');
            console error(err);
        }
    };

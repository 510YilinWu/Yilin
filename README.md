# Yilin
# summary of the participant’s mean performance for each contrast in two-interval forced choice (2IFC) detection task
# Matlab file
-------------------------------------start from here--------------------------------------------------------
%Part 1. 
%load the provided data file (testData.mat). 
load('testData.mat')

%%
%Part 2. 
%two variables: 
    %1)contNeuron: the contrast of a visual stimulus that was repeatedly presented to a neuron 
    %2)respNeuron: the average response (in spikes/s) of that neuron to each of the visual stimulus contrasts

%scatter plot the "contrast response function” for this neuron
%plot best fit line (see the end of file)
figure( 'Name', 'contrast response function from Basic Fitting' );
contrast_response_function_plot=scatter(contNeuron,respNeuron,100,"black","filled");
title('contrast response function','FontSize',30)
xlabel('contrast(%)','FontSize',25) 
ylabel('Single Neuron average response (spikes/s)','FontSize',25)

%%
% plot shows that these two variables have a positive association
%Because as the contrast of a visual stimulus increases, the average response (in spikes/s) of that neuron to each of the visual stimulus contrasts increased. 

%the neuron's contrast response function is a sigmoid curve.
%They donht fire at all for very low contrasts, but they rapidly increase their firing rate when reach certain threshold. 
% Once reach their maximum rates, the firing rate tends to level off.

%Use the parameter correlation coefficient to measure the property of how the contrast affects the response of this neuron
%correlation coefficient (r)=0.9238, Therefore, contrast significant positively affect the activity of this neuron.
r=corrcoef(contNeuron,respNeuron);
save r

%%
%Part 3. 
%Two variables
    %1)contHuman contains the contrast on each trial, in the order that they were presented
    %2)respHuman contains whether the participant correctly chose the interval in which the stimulus was presented, in the order that the stimuli were presented. 
        % 0  = incorrect, 1 = correct

%How many unique contrasts were presented and how many times was each contrast repeated?
    % x=potential unique contrast, range 0%~100%
    % count=number of unique contrasts were presented
    % unique_contrast_x_repeated_times=each contrast repeated times
    % all_unique_contrasts=all unique contrasts
count=0;
for x=0:100
    unique_contrast_x_repeated_times=sum(contHuman==x);
    
    % if unique_contrast_x_repeated_times~=0, x is a unique contrast
    % save the unique contrast into all_unique_contrasts
    if unique_contrast_x_repeated_times~=0
        all_unique_contrasts(count+1)=x;
        save all_unique_contrasts

            %check if all unique contrasts repeated times are same, if so, print the summary
            if unique_contrast_x_repeated_times==unique_contrast_x_repeated_times
                if x==100
                    disp("total "+(count+1)+" unique contrasts, all repeated times are same"+", repeat "+unique_contrast_x_repeated_times+" times.")
                    disp ("All unique contrasts are :")
                    disp (all_unique_contrasts)
                end
            end
            if unique_contrast_x_repeated_times~=unique_contrast_x_repeated_times
                disp(unique_contrast_x_repeated_times)
            end

        %update count
        count=count+1;
    end
end

%%
%plot a summary of the participant mean performance for each contrast
%Save & link individual contrast and individual participant performance for 90 trials in contHuman_respHuman

%value = sum of individual participant performance (0  =incorrect, 1 = correct)
%times = time of the unique_contrast x already showed
contHuman_respHuman=[reshape(contHuman.', [], 1), reshape(respHuman.', [], 1)];
test_column_value_of_unique_contrast=1;
for column=1:count
    value=0;
    times=0;
    
    %find the unique_contrast in total 90 trials,if find, add 1 to times.
    %update value. (incorrect=+0,correct=+1)
    for i=1:90
        if all_unique_contrasts(1,test_column_value_of_unique_contrast)==contHuman_respHuman(i,1)
            times=times+1;
            if contHuman_respHuman(i,2)==1
                value=value+1;
            end
            if contHuman_respHuman(i,2)==0
                value=value+0;
            end
            %if time of the unique_contrast x already showed equals to
            %total times the unique_contrast showed in trials,divide by 10
            %to get mean performance for each contrast
            if times==unique_contrast_x_repeated_times
                mean(test_column_value_of_unique_contrast)=value/unique_contrast_x_repeated_times;
                save mean
            end
        end
    end
    %updated test_column_value
    test_column_value_of_unique_contrast=test_column_value_of_unique_contrast+1;
end

%Save & link each contrast and participant s mean performance for each contrast summary in mean_performance_for_each_contrast
mean_performance_for_each_contrast=[reshape(all_unique_contrasts.', [], 1), reshape(mean.', [], 1)]
save mean_performance_for_each_contrast

%plot a summary of the participant mean performance for each contrast
%plot best fit line (see the end of file)
figure( 'Name', 'summary of the participants mean performance for each contrast from Basic Fitting' );
participant_mean_performance_for_each_contrast=scatter(mean_performance_for_each_contrast(:,1),mean_performance_for_each_contrast(:,2),100,"black","filled");
title('summary of the participants mean performance for each contrast','FontSize',30)
xlabel('contrast(%)','FontSize',25) 
ylabel('mean performance','FontSize',25)

%% Fit: 'contrast response function'.
[xData, yData] = prepareCurveData( contNeuron, respNeuron );

% Set up fittype and options.
ft = fittype( 'poly3' );

% Fit model to data.
[fitresult, gof] = fit( xData, yData, ft );

% Plot fit with data.
figure( 'Name', 'contrast response function from curve Fitter' );
plot( fitresult, xData, yData );
% Label axes
title('contrast response function','FontSize',30)
xlabel( 'contrast(%)', 'Interpreter', 'none' );
ylabel( 'Single Neuron average response (spikes/s)', 'Interpreter', 'none' );
grid on
%% Fit: 'summary of the participants mean performance for each contrast'.
[xData, yData] = prepareCurveData( all_unique_contrasts, mean );

% Set up fittype and options.
ft = fittype( 'poly3' );

% Fit model to data.
[fitresult, gof] = fit( xData, yData, ft, 'Normalize', 'on' );

% Plot fit with data.
figure( 'Name', 'summary of the participants mean performance for each contrast from curve Fitter' );
plot( fitresult, xData, yData );
% Label axes
title('summary of the participants mean performance for each contrast','FontSize',30)
xlabel( 'contrast(%)', 'Interpreter', 'none' );
ylabel( 'mean performance', 'Interpreter', 'none' );
grid on
